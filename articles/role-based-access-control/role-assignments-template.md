---
title: Azure-szerepkörök kiosztása Azure Resource Manager-sablonok használatával – Azure RBAC
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz Azure Resource Manager sablonokkal és az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: 65b4ec369085e44cdffb0550e9eeaef0196cd35a
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556027"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Azure-szerepkörök kiosztása Azure Resource Manager-sablonok használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] A Azure PowerShell vagy az Azure CLI használata mellett szerepköröket is hozzárendelhet [Azure Resource Manager sablonok](../azure-resource-manager/templates/template-syntax.md)használatával. A sablonok akkor lehetnek hasznosak, ha az erőforrásokat következetesen és ismételten kell telepíteni. Ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket sablonok használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Objektum-azonosítók beolvasása

Szerepkör hozzárendeléséhez meg kell adnia annak a felhasználónak, csoportnak vagy alkalmazásnak az AZONOSÍTÓját, amelyhez hozzá szeretné rendelni a szerepkört. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111` . Az azonosítót a Azure Portal, Azure PowerShell vagy az Azure CLI használatával szerezheti be.

### <a name="user"></a>User

A felhasználók AZONOSÍTÓjának lekéréséhez használhatja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) vagy [az ad User show](/cli/azure/ad/user#az-ad-user-show) parancsokat.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group

Egy csoport AZONOSÍTÓjának lekéréséhez használhatja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) vagy [az ad Group show](/cli/azure/ad/group#az-ad-group-show) parancsokat.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Felügyelt identitások

A felügyelt identitás AZONOSÍTÓjának lekéréséhez a [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) vagy [az ad SP](/cli/azure/ad/sp) parancsokat használhatja.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Alkalmazás

Egy egyszerű szolgáltatásnév (az alkalmazás által használt identitás) AZONOSÍTÓjának lekéréséhez használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) vagy [az ad SP List](/cli/azure/ad/sp#az-ad-sp-list) parancsot. Egyszerű szolgáltatásnév esetén használja az objektumazonosító azonosítót, **ne** pedig az alkalmazás azonosítóját.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Azure-szerepkör kiosztása

Az Azure RBAC a hozzáférés biztosításához rendeljen hozzá egy szerepkört.

### <a name="resource-group-scope-without-parameters"></a>Erőforráscsoport hatóköre (paraméterek nélkül)

A következő sablon a szerepkörök hozzárendelésének alapszintű módját mutatja be. Néhány érték a sablonban van megadva. A következő sablon a következőket mutatja be:

-  Az [olvasó](built-in-roles.md#reader) szerepkör társítása egy felhasználóhoz, csoporthoz vagy alkalmazáshoz erőforráscsoport-hatókörben

A sablon használatához a következőket kell tennie:

- Új JSON-fájl létrehozása és a sablon másolása
- A helyére írja be annak a `<your-principal-id>` felhasználónak, csoportnak, felügyelt identitásnak vagy alkalmazásnak az azonosítóját, amelyhez a szerepkört hozzá szeretné rendelni

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

Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) parancsait mutatja be a telepítés elindításához egy ExampleGroup nevű erőforráscsoporthoz.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Az alábbi példa az olvasói szerepkör hozzárendelését mutatja be egy erőforráscsoport felhasználói számára a sablon telepítése után.

![Szerepkör-hozzárendelés erőforrás-csoport hatókörében](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Erőforráscsoport vagy előfizetés hatóköre

Az előző sablon nem túl rugalmas. A következő sablon paramétereket használ, és különböző hatókörökben használható. A következő sablon a következőket mutatja be:

- Szerepkör társítása felhasználóhoz, csoporthoz vagy alkalmazáshoz erőforráscsoport vagy előfizetés hatókörében
- Tulajdonos, közreműködő és olvasó szerepkörök meghatározása paraméterként

A sablon használatához a következő bemeneteket kell megadnia:

- Annak a felhasználónak, csoportnak, felügyelt identitásnak vagy alkalmazásnak az azonosítója, amelyhez a szerepkört hozzá szeretné rendelni
- Egyedi azonosító, amely a szerepkör-hozzárendeléshez lesz használva, vagy használhatja az alapértelmezett azonosítót.

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
> Ez a sablon nem idempotens, kivéve, ha ugyanazt az `roleNameGuid` értéket paraméterként megadja a sablon minden egyes telepítéséhez. Ha nincs `roleNameGuid` megadva, a rendszer alapértelmezés szerint új GUID azonosítót hoz létre minden központi telepítésnél, és a további központi telepítések `Conflict: RoleAssignmentExists` hibát jeleznek.

A szerepkör-hozzárendelés hatóköre a központi telepítés szintjétől függ. Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) parancsait mutatja be a telepítés elindításához egy erőforráscsoport-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Az alábbi példa a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) és az [Deployment sub Create](/cli/azure/deployment/sub#az_deployment_sub_create) parancsait mutatja be a központi telepítés előfizetési hatókörben való elindításához és a hely megadásához.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Erőforrás hatóköre

Ha egy erőforrás szintjén kell szerepkört hozzárendelni, állítsa a `scope` szerepkör-hozzárendelés tulajdonságát az erőforrás nevére.

A következő sablon a következőket mutatja be:

- Új tárfiók létrehozása
- Szerepkör társítása felhasználóhoz, csoporthoz vagy alkalmazáshoz a Storage-fiók hatókörében
- Tulajdonos, közreműködő és olvasó szerepkörök meghatározása paraméterként

A sablon használatához a következő bemeneteket kell megadnia:

- Annak a felhasználónak, csoportnak, felügyelt identitásnak vagy alkalmazásnak az azonosítója, amelyhez a szerepkört hozzá szeretné rendelni

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

Az előző sablon üzembe helyezéséhez használja az erőforráscsoport-parancsokat. Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) parancsait mutatja be a telepítés elindításához egy erőforrás-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

A következő példa a közreműködői szerepkör hozzárendelését mutatja be egy felhasználónak egy Storage-fiókhoz a sablon telepítése után.

![Szerepkör-hozzárendelés erőforrás-hatókörben](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Új egyszerű szolgáltatásnév

Ha létrehoz egy új szolgáltatásnevet, és azonnal megpróbál hozzárendelni egy szerepkört az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés bizonyos esetekben sikertelen lehet. Ha például létrehoz egy új felügyelt identitást, majd megpróbál hozzárendelni egy szerepkört az adott szolgáltatáshoz ugyanahhoz a Azure Resource Manager-sablonhoz, előfordulhat, hogy a szerepkör-hozzárendelés sikertelen lesz. A hiba oka valószínűleg a replikálás késése. Az egyszerű szolgáltatás egy régióban jön létre; a szerepkör-hozzárendelés azonban egy másik régióban is előfordulhat, amely még nem replikálta a szolgáltatásnevet.

Ennek a forgatókönyvnek a megoldásához a `principalType` tulajdonságot a `ServicePrincipal` szerepkör-hozzárendelés létrehozásakor kell beállítania. A szerepkör-hozzárendelést is be kell állítania `apiVersion` `2018-09-01-preview` vagy később.

A következő sablon a következőket mutatja be:

- Új felügyelt identitási szolgáltatásnév létrehozása
- A `principalType`
- A közreműködői szerepkör társítása az adott egyszerű szolgáltatáshoz erőforráscsoport-hatókörben

A sablon használatához a következő bemeneteket kell megadnia:

- A felügyelt identitás alapneve, vagy használhatja az alapértelmezett karakterláncot is.

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

Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) parancsait mutatja be a telepítés elindításához egy erőforráscsoport-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Az alábbi példa a közreműködői szerepkör hozzárendelését mutatja be egy új felügyelt identitási szolgáltatásnév számára a sablon telepítése után.

![Szerepkör-hozzárendelés egy új felügyelt identitás egyszerű szolgáltatásnév számára](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: ARM-sablonok létrehozása és üzembe helyezése a Azure Portal használatával](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Az ARM-sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/templates/template-syntax.md)
- [Erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?term=rbac)
