---
title: Egyéni Azure-szerepkör létrehozása Azure Resource Manager sablon használatával – Azure RBAC
description: Ismerje meg, hogyan hozhat létre Azure-beli egyéni szerepkört egy Azure Resource Manager sablon (ARM-sablon) és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: 96dfdc0a1c32237c55d4e65bb25989656e2a4ad2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097022"
---
# <a name="create-an-azure-custom-role-using-an-arm-template"></a>Egyéni Azure-szerepkör létrehozása ARM-sablonnal

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni szerepköröket](custom-roles.md)is. Ez a cikk azt ismerteti, hogyan hozhat létre egyéni szerepkört egy Azure Resource Manager sablon (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Egyéni szerepkör létrehozásához meg kell adnia a szerepkör nevét, az engedélyeket, valamint a szerepkör használatát. Ebben a cikkben egy _Egyéni szerepkör-RG olvasó_ nevű szerepkört hoz létre olyan erőforrás-engedélyekkel, amelyek az előfizetések hatókörében vagy alacsonyabb szinten rendelhetők el.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkör létrehozásához a következőket kell tennie:

- Egyéni szerepkörök, például [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés-rendszergazda](built-in-roles.md#user-access-administrator)létrehozásához szükséges engedélyek.

## <a name="review-the-template"></a>A sablon áttekintése

A cikkben használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/create-role-def)származik. A sablonban négy paraméter és egy erőforrás szakasz található. A négy paraméter a következők:

- Az alapértelmezett értékkel rendelkező műveletek tömbje `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- `notActions`Üres alapértelmezett értékkel rendelkező tömb.
- A szerepkör neve alapértelmezett értékkel `Custom Role - RG Reader` .
- A szerepkör leírása alapértelmezett értékkel `Subscription Level Deployment of a Role Definition` .

Az ehhez az egyéni szerepkörhöz hozzárendelni kívánt hatókör az aktuális előfizetésre van beállítva.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

A sablonban definiált erőforrás:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az előző sablon üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg Azure Cloud Shell a PowerShell számára.

1. Másolja és illessze be a következő szkriptet a Cloud Shellba.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Adja meg a központi telepítés helyét (például *CentralUS* ).

1. Adja meg az egyéni szerepkörhöz tartozó műveletek listáját vesszővel tagolt listaként, mint például a *Microsoft. Resources/Resources/READ, Microsoft. Resources/Subscriptions/resourceGroups/Read* .

1. Ha szükséges, nyomja le az ENTER billentyűt a parancs futtatásához `New-AzDeployment` .

    A [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) parancs telepíti a sablont az egyéni szerepkör létrehozásához.

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az alábbi lépéseket követve ellenőrizheti, hogy létrejött-e az egyéni szerepkör.

1. Futtassa a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot az egyéni szerepkör listázásához.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. A Azure Portal nyissa meg az előfizetését.

1. A bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.

1. Válassza a **szerepkörök** fület.

1. Adja meg a **típus** listát a **CustomRole** értékre.

1. Győződjön meg arról, hogy az **Egyéni szerepkör-RG olvasó** szerepkör szerepel a listáján.

   ![Új egyéni szerepkör a Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

Az egyéni szerepkör eltávolításához kövesse az alábbi lépéseket.

1. A következő parancs futtatásával távolítsa el az egyéni szerepkört.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Az **Y** érték megadásával erősítse meg, hogy el kívánja távolítani az egyéni szerepkört.

## <a name="next-steps"></a>Következő lépések

- [Az Azure szerepkör-definíciók ismertetése](role-definitions.md)
- [Gyors útmutató: Azure-beli szerepkör-hozzárendelés hozzáadása Azure Resource Manager sablon használatával](quickstart-role-assignments-template.md)
- [ARM-sablon dokumentációja](../azure-resource-manager/templates/index.yml)
