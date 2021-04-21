---
title: Azure-szerepkörök hozzárendelése Azure Resource Manager használatával – Azure RBAC
description: Megtudhatja, hogyan adhat hozzáférést Azure-erőforrásokhoz felhasználók, csoportok, szolgáltatásnév vagy felügyelt identitások számára Azure Resource Manager sablonok és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: ba1df23b40de82a8ef901541884ef29ea0b504a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771874"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Azure-szerepkörök hozzárendelése Azure Resource Manager sablonokkal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]A Azure PowerShell vagy az Azure CLI használata mellett szerepköröket is hozzárendelhet a [Azure Resource Manager használatával.](../azure-resource-manager/templates/template-syntax.md) A sablonok akkor lehetnek hasznosak, ha folyamatosan és ismételten üzembe kell helyeznie az erőforrásokat. Ez a cikk a szerepkörök sablonok használatával való hozzárendelését ismerteti.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Objektum-edik lekért objektum-

Szerepkör hozzárendelése esetén meg kell adnia annak a felhasználónak, csoportnak vagy alkalmazásnak az azonosítóját, akihez hozzá szeretné rendelni a szerepkört. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111` . Az azonosítót a következő használatával kaphatja meg: Azure Portal, Azure PowerShell Azure CLI.

### <a name="user"></a>Felhasználó

Egy felhasználó azonosítójának lekért használhatja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) vagy [az az ad user show](/cli/azure/ad/user#az_ad_user_show) parancsot.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group

Egy csoport azonosítójának lekért azonosítóját a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) vagy [az az ad group show](/cli/azure/ad/group#az_ad_group_show) paranccsal kaphatja meg.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Felügyelt identitások

Egy felügyelt identitás azonosítójának lekért azonosítóját a [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) vagy [az az ad sp](/cli/azure/ad/sp) paranccsal kaphatja meg.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Alkalmazás

Egy szolgáltatásnév azonosítójának (egy alkalmazás által használt identitásnak) az azonosítóját a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) vagy [az az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) paranccsal kaphatja meg. Szolgáltatásnév esetén az objektumazonosítót használja, **ne az** alkalmazásazonosítót.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Azure-szerepkör hozzárendelése

Az Azure RBAC-ban a hozzáférés megadásához hozzá kell rendelnie egy szerepkört.

### <a name="resource-group-scope-without-parameters"></a>Erőforráscsoport hatóköre (paraméterek nélkül)

Az alábbi sablon a szerepkörök hozzárendelésének alapvető módját mutatja be. Egyes értékek a sablonon belül vannak megadva. Az alábbi sablon a következőket mutatja be:

-  Olvasó szerepkör [hozzárendelése](built-in-roles.md#reader) felhasználóhoz, csoporthoz vagy alkalmazáshoz egy erőforráscsoporti hatókörben

A sablont a következő lépésekben használhatja:

- Új JSON-fájl létrehozása és a sablon másolása
- Cserélje `<your-principal-id>` le a helyére egy felhasználó, csoport, felügyelt identitás vagy alkalmazás azonosítóját, amelyhez hozzárendeli a szerepkört

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Íme a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) parancs, amelyek segítségével elindíthatja az üzembe helyezést egy ExampleGroup nevű erőforráscsoportban.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Az alábbiakban egy példát mutatunk be az Olvasó szerepkör-hozzárendelésre egy erőforráscsoport felhasználója számára a sablon üzembe helyezése után.

![Szerepkör-hozzárendelés erőforráscsoporti hatókörben](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Erőforráscsoport vagy előfizetés hatóköre

Az előző sablon nem túl rugalmas. Az alábbi sablon paramétereket használ, és különböző hatókörökben használható. Az alábbi sablon a következőket mutatja be:

- Szerepkör hozzárendelése felhasználóhoz, csoporthoz vagy alkalmazáshoz egy erőforráscsoport vagy előfizetés hatókörében
- Tulajdonos, közreműködő és olvasó szerepkör megadása paraméterként

A sablon használata esetén a következő bemeneteket kell megadnia:

- Annak a felhasználónak, csoportnak, felügyelt identitásnak vagy alkalmazásnak az azonosítója, akihez a szerepkört hozzá kell rendelni
- A szerepkör-hozzárendeléshez használt egyedi azonosító, vagy használhatja az alapértelmezett azonosítót

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Ez a sablon csak akkor idempotent, ha a sablon minden üzembe helyezéséhez ugyanazt az értéket `roleNameGuid` paraméterként biztosítja. Ha nem ad meg értéket, a rendszer alapértelmezés szerint új GUID-azonosítót hoz létre minden üzemelő példányon, és a későbbi üzembe helyezések `roleNameGuid` egy hibával `Conflict: RoleAssignmentExists` meghiúsulnak.

A szerepkör-hozzárendelés hatókörét a központi telepítés szintjétől kell meghatározni. Íme a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) parancs, amelyek segítségével elindíthatja az üzembe helyezést egy erőforráscsoporti hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Íme a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) és [az az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) példaparancs az üzembe helyezés előfizetési hatókörben való elkezdéséhez és a hely megadásához.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Erőforrás hatóköre

Ha egy szerepkört egy erőforrás szintjén kell hozzárendelni, állítsa a szerepkör-hozzárendelés tulajdonságát az erőforrás `scope` nevére.

Az alábbi sablon a következőket mutatja be:

- Új tárfiók létrehozása
- Szerepkör hozzárendelése felhasználóhoz, csoporthoz vagy alkalmazáshoz a tárfiók hatókörében
- Tulajdonos, közreműködő és olvasó szerepkör megadása paraméterként

A sablonhoz a következő bemeneteket kell megadnia:

- Annak a felhasználónak, csoportnak, felügyelt identitásnak vagy alkalmazásnak az azonosítója, akihez hozzárendeli a szerepkört

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Az előző sablon üzembe helyezéséhez használja az erőforráscsoport-parancsokat. Az alábbi példában [a New-AzResourceGroupDeployment és](/powershell/module/az.resources/new-azresourcegroupdeployment) [az az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) parancsokat mutatjuk be az üzembe helyezés erőforrás-hatókörben való elkezdéséhez.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Az alábbiakban egy példát mutatunk be a közreműködői szerepkör-hozzárendelésre egy tárfiók felhasználója számára a sablon üzembe helyezése után.

![Szerepkör-hozzárendelés erőforrás-hatókörben](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Új szolgáltatásnév

Ha létrehoz egy új szolgáltatásnévt, és azonnal megpróbál hozzárendelni egy szerepkört a szolgáltatásnévhez, a szerepkör-hozzárendelés bizonyos esetekben meghiúsulhat. Ha például létrehoz egy új felügyelt identitást, majd megpróbál hozzárendelni egy szerepkört a szolgáltatásnévhez ugyanabban a Azure Resource Manager-sablonban, előfordulhat, hogy a szerepkör-hozzárendelés sikertelen lesz. A hiba oka valószínűleg a replikáció késése. A szolgáltatásnév egy régióban jön létre; A szerepkör-hozzárendelés azonban egy másik régióban is előfordulhat, amely még nem replikálta a szolgáltatásnévvel.

A forgatókönyv megoldásához a tulajdonságot a következőre kell `principalType` `ServicePrincipal` beállítania a szerepkör-hozzárendelés létrehozásakor: . A szerepkör-hozzárendeléshez a `apiVersion` vagy újabb szerepkör-hozzárendelést `2018-09-01-preview` is be kell állítania.

Az alábbi sablon a következőket mutatja be:

- Új felügyelt identitás szolgáltatásnév létrehozása
- A következő megadása: `principalType`
- Közreműködői szerepkör hozzárendelése a szolgáltatásnévhez egy erőforráscsoporti hatókörben

A sablon használata esetén a következő bemeneteket kell megadnia:

- A felügyelt identitás alapneve, vagy használhatja az alapértelmezett sztringet

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Az alábbi példában [a New-AzResourceGroupDeployment és](/powershell/module/az.resources/new-azresourcegroupdeployment) [az az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) parancsokat mutatjuk be az üzembe helyezés erőforráscsoporti hatókörben való elkezdéséhez.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Az alábbiakban egy példát mutatunk be a közreműködői szerepkör-hozzárendelésre egy új felügyelt identitás szolgáltatásnévhez a sablon üzembe helyezése után.

![Szerepkör-hozzárendelés új felügyelt identitás szolgáltatásnévhez](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: ARM-sablonok létrehozása és üzembe helyezése a Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Az ARM-sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/templates/template-syntax.md)
- [Erőforráscsoportok és erőforrások létrehozása az előfizetés szintjén](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?term=rbac)
