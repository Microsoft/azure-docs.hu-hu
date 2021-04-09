---
title: 'Gyors útmutató: Azure-szerepkör kiosztása Azure Resource Manager sablon használatával – Azure RBAC'
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz az erőforráscsoport-hatókörben lévő felhasználók számára Azure Resource Manager sablonok és Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.custom: subject-armqs
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 6e8f194cd85a3c381bdabf206777a99dce3c29b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559262"
---
# <a name="quickstart-assign-an-azure-role-using-an-arm-template"></a>Gyors útmutató: Azure-szerepkör kiosztása ARM-sablon használatával

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ebben a rövid útmutatóban létrehoz egy erőforráscsoportot, és felhasználói hozzáférést biztosít a virtuális gépek létrehozásához és kezeléséhez az erőforráscsoporthoz. Ez a rövid útmutató egy Azure Resource Manager sablont (ARM-sablont) használ a hozzáférés biztosításához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-rbac-builtinrole-resourcegroup%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szerepkörök hozzárendeléséhez és a szerepkör-hozzárendelések eltávolításához a következőket kell tennie:

- Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)
- A szerepkörök hozzárendeléséhez három elemet kell megadnia: rendszerbiztonsági tag, szerepkör-definíció és hatókör. Ebben a rövid útmutatóban a rendszerbiztonsági tag Ön vagy egy másik felhasználó a címtárban, a szerepkör-definíció a [virtuális gép közreműködője](built-in-roles.md#virtual-machine-contributor), a hatókör pedig egy Ön által megadott erőforráscsoport.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/) közül származik. A sablon három paramétert és egy erőforrás szakaszt tartalmaz. A Resources (erőforrások) szakaszban figyelje meg, hogy a szerepkör-hozzárendelés három eleme van: rendszerbiztonsági tag, szerepkör-definíció és hatókör.

:::code language="json" source="~/quickstart-templates/101-rbac-builtinrole-resourcegroup/azuredeploy.json":::

A sablonban definiált erőforrás:

- [Microsoft. Authorization/roleAssignments](/azure/templates/Microsoft.Authorization/roleAssignments)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Határozza meg az Azure-előfizetéséhez társított e-mail-címet. Vagy határozza meg a címtár egy másik felhasználójának e-mail-címét.

1. Nyissa meg Azure Cloud Shell a PowerShell számára.

1. Másolja és illessze be a következő szkriptet a Cloud Shellba.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name (i.e. ExampleGrouprg)"
    $emailAddress = Read-Host -Prompt "Enter an email address for a user in your directory"
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    
    $roleAssignmentName = New-Guid
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    $roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"
    
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    ```

1. Adjon meg egy erőforráscsoport-nevet, például ExampleGrouprg.

1. Adjon meg egy e-mail-címet saját maga vagy egy másik felhasználó számára a címtárban.

1. Adja meg az erőforráscsoport helyét (például CentralUS).

1. Ha szükséges, nyomja le az ENTER billentyűt az New-AzResourceGroupDeployment parancs futtatásához.

    A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs létrehoz egy új erőforráscsoportot, és a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancs telepíti a sablont a szerepkör-hozzárendelés hozzáadásához.

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```azurepowershell
    PS> New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    
    DeploymentName          : azuredeploy
    ResourceGroupName       : ExampleGrouprg
    ProvisioningState       : Succeeded
    Timestamp               : 5/22/2020 9:01:30 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name                  Type                       Value
                              ====================  =========================  ==========
                              roleAssignmentName    String                     {roleAssignmentName}
                              roleDefinitionID      String                     9980e02c-c2be-4d73-94e8-173b1dc7cf3c
                              principalId           String                     {principalId}
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. A Azure Portal nyissa meg a létrehozott erőforráscsoportot.

1. A bal oldali menüben kattintson a **hozzáférés-vezérlés (iam)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** fülre.

1. Ellenőrizze, hogy a **virtuális gépi közreműködő** szerepkör hozzá van-e rendelve a megadott felhasználóhoz.

   ![Új szerepkör hozzárendelése](./media/quickstart-role-assignments-template/role-assignment-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott szerepkör-hozzárendelés és erőforráscsoport eltávolításához kövesse az alábbi lépéseket.

1. Másolja és illessze be a következő szkriptet a Cloud Shellba.

    ```azurepowershell
    $emailAddress = Read-Host -Prompt "Enter the email address of the user with the role assignment to remove"
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name to remove (i.e. ExampleGrouprg)"
    
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    
    Remove-AzRoleAssignment -ObjectId $principalId -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName $resourceGroupName
    Remove-AzResourceGroup -Name $resourceGroupName
    ```
    
1. Adja meg a felhasználó e-mail-címét az eltávolítani kívánt szerepkör-hozzárendeléssel.

1. Adja meg az eltávolítani kívánt erőforráscsoport nevét (például ExampleGrouprg).

1. Ha szükséges, nyomja le az ENTER billentyűt az Remove-AzResourceGroup parancs futtatásához.

1. Az **Y** érték megadásával erősítse meg, hogy el kívánja távolítani az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)