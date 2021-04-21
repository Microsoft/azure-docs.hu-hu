---
title: Erőforrások üzembe helyezése a felügyeleti csoportban
description: Ismerteti, hogyan helyezhet üzembe erőforrásokat a felügyeleti csoport hatókörében egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 74e00921a1170a7750f4a2d239bb778150ac2cae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781936"
---
# <a name="management-group-deployments-with-arm-templates"></a>Felügyeleti csoportok üzembe helyezése ARM-sablonokkal

Ahogy a szervezet kiforrott, üzembe helyezhet egy Azure Resource Manager-sablont (ARM-sablont), hogy a felügyeleti csoport szintjén hozzon létre erőforrásokat. Előfordulhat például, hogy szabályzatokat [](../../governance/policy/overview.md) vagy [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) kell meghatároznia és hozzárendelni egy felügyeleti csoporthoz. A felügyeleticsoport-szintű sablonokkal deklaratív módon alkalmazhat házirendeket, és szerepköröket rendelhet hozzá a felügyeleti csoport szintjén.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípus helyezhető üzembe a felügyeleti csoport szintjén. Ez a szakasz felsorolja a támogatott erőforrástípusokat.

A Azure Blueprints használja a következőt:

* [Leletek](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Tervrajz](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments (terv hozzárendelései)](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Verziók](/azure/templates/microsoft.blueprint/blueprints/versions)

A Azure Policy használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions (szabályzatdefiníciók)](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szervizelések](/azure/templates/microsoft.policyinsights/remediations)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatáshoz használja a következőt:

* [roleAssignments (szerepkör hozzárendelései)](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions (szerepkördefiníciók)](/azure/templates/microsoft.authorization/roledefinitions)

Az előfizetésekbe vagy erőforráscsoportokba üzembe helyezett beágyazott sablonokhoz használja a következőt:

* [Telepítések](/azure/templates/microsoft.resources/deployments)

Az erőforrások kezeléséhez használja a következőt:

* [Címkék](/azure/templates/microsoft.resources/tags)

A felügyeleti csoportok bérlői szintű erőforrások. Felügyeleti csoportokat azonban létrehozhat a felügyeleti csoportok központi telepítésében, ha az új felügyeleti csoport hatókörét a bérlőre stb. Lásd: [Felügyeleti csoport.](#management-group)

## <a name="schema"></a>Séma

A felügyeleti csoportok üzembe helyezéséhez használt séma eltér az erőforráscsoportok üzembe helyezéséhez használt sémától.

Sablonokhoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

A paraméterfájl sémája minden üzembe helyezési hatókörben ugyanaz. Paraméterfájlokhoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

Felügyeleti csoportban való üzembe helyezéshez használja a felügyeleti csoport üzembe helyezési parancsokat.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-hez használja [az az deployment mg create szolgáltatásokat:](/cli/azure/deployment/mg#az_deployment_mg_create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Azure PowerShell használja a [New-AzManagementGroupDeployment et.](/powershell/module/az.resources/new-azmanagementgroupdeployment)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Az üzembe helyezési parancsokkal és az ARM-sablonok üzembe helyezésének beállításaival kapcsolatos további információkért lásd:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-val](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Sablonok üzembe helyezése GitHub-adattárból üzembe helyezési gombbal](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése a Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Üzembe helyezés helye és neve

A felügyeleti csoport szintű központi telepítések esetén meg kell adnia a központi telepítés helyét. Az üzembe helyezés helye elkülönül az üzembe helyezett erőforrások helyétől. Az üzembe helyezési hely határozza meg, hogy hol tárolja az üzembe helyezési adatokat. [Az](deploy-to-subscription.md) [előfizetések és a bérlők](deploy-to-tenant.md) üzembe helyezéséhez is szükség van egy helyre. Az [erőforráscsoportok üzemelő](deploy-to-resource-group.md) példányai esetén az erőforráscsoport helye az üzembe helyezési adatok tárolására használatos.

Meg kell adnia a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet. Az alapértelmezett név a sablonfájl neve. Ha például üzembe helyez egyazuredeploy.js _nevű sablont,_ létrejön egy **azuredeploy nevű alapértelmezett üzemelő példánynév.**

A hely minden üzembe helyezési név esetén nem módosítható. Nem hozhat létre üzemelő példányokat egy helyen, ha már létezik azonos nevű üzemelő példány egy másik helyen. Ha például létrehoz egy felügyeleti csoport üzemelő példányát **deployment1** néven a **centralusban,** később nem hozhat létre másik üzemelő példányt **deployment1** néven, de a **westus** helyen. Ha a hibakód jelenik meg, vagy használjon egy másik nevet, vagy ugyanazt a helyet, mint az előző üzemelő `InvalidDeploymentLocation` példány a névhez.

## <a name="deployment-scopes"></a>Üzembe helyezési hatókörök

Felügyeleti csoportban való üzembe helyezéskor a következő erőforrásokat helyezheti üzembe:

* a cél felügyeleti csoport a műveletből
* egy másik felügyeleti csoport a bérlőben
* előfizetések a felügyeleti csoportban
* erőforráscsoportok a felügyeleti csoportban
* az erőforráscsoport bérlője

A [bővítményerőforrás](scope-extension-resources.md) hatóköre az üzembe helyezési céltól eltérő célra is kiterjedhet.

A sablont üzembe helyező felhasználónak hozzá kell férnie a megadott hatókörre.

Ez a szakasz a különböző hatókörök megadását mutatja be. Ezeket a különböző hatókörök egyetlen sablonban egyesítheti.

### <a name="scope-to-target-management-group"></a>Hatókör cél felügyeleti csoportra

A sablon resources (erőforrások) szakaszában meghatározott erőforrások az üzembe helyezési paranccsal alkalmazhatók a felügyeleti csoportra.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Hatókör másik felügyeleti csoportra

Egy másik felügyeleti csoport megcélhoz adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot. Állítsa a `scope` tulajdonságot egy értékre a következő formátumban: `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Hatókör előfizetéshez

Felügyeleti csoporton belüli előfizetéseket is megcélhat. A sablont üzembe helyező felhasználónak hozzá kell férnie a megadott hatókörre.

A felügyeleti csoporton belüli előfizetések megcélhoz használjon beágyazott üzemelő példányokat és a `subscriptionId` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Hatókör erőforráscsoportra

A felügyeleti csoporton belül erőforráscsoportokat is megcélhat. A sablont üzembe helyező felhasználónak hozzá kell férnie a megadott hatókörre.

Ha a felügyeleti csoporton belül egy erőforráscsoportot céloz meg, használjon beágyazott üzemelő példányokat. Állítsa be a `subscriptionId` és a `resourceGroup` tulajdonságot. Ne állítsa be a beágyazott üzemelő példány helyét, mert az az erőforráscsoport helyén van üzembe állítva.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Ha felügyeleti csoport üzemelő példányát használja egy erőforráscsoport előfizetésen belüli létrehozásához és egy tárfióknak az adott erőforráscsoportban való üzembe helyezéséhez, tekintse meg a Következőt: Üzembe helyezés előfizetésben és [erőforráscsoportban.](#deploy-to-subscription-and-resource-group)

### <a name="scope-to-tenant"></a>Hatókör bérlőre

Ha erőforrásokat hoz létre a bérlőben, állítsa a `scope` következőt: `/` . A sablont üzembe helyező felhasználónak a bérlőn való üzembe helyezéshez [szükséges hozzáféréssel kell lennie.](deploy-to-tenant.md#required-access)

Beágyazott üzembe helyezéshez állítsa be a és a `scope` `location` halmazt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

A hatókört egyes erőforrástípusokra, például felügyeleti csoportokra `/` is beállíthatja. Az új felügyeleti csoport létrehozását a következő szakaszban ismertetjük.

## <a name="management-group"></a>Felügyeleti csoport

Felügyeleti csoport felügyeleti csoport központi telepítésében való létrehozásához a felügyeleti csoport hatókörét a következőre kell `/` beállítania: .

Az alábbi példa egy új felügyeleti csoportot hoz létre a gyökér felügyeleti csoportban.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

A következő példa egy új felügyeleti csoportot hoz létre a szülőként megadott felügyeleti csoportban. Figyelje meg, hogy a hatókör beállítása `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Előfizetések

Ha ARM-sablonnal hoz létre új Azure-előfizetést egy felügyeleti csoportban, tekintse meg a következőt:

* [Azure-előfizetések Nagyvállalati Szerződés létrehozása](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Azure-előfizetések létrehozása programozott módon egy Microsoft Ügyfélszerződés](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Azure-előfizetések létrehozása programozott módon egy Microsoft Partnerszerződés](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Meglévő Azure-előfizetést új felügyeleti csoportba áthelyező sablon üzembe helyezéséhez lásd: Előfizetések áthelyezése [AZ ARM-sablonban](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

A felügyeleti csoportra telepített egyéni szabályzatdefiníciók a felügyeleti csoport bővítményei. Egy egyéni szabályzatdefiníció azonosítójának lekért értékhez használja az [extensionResourceId()](template-functions-resource.md#extensionresourceid) függvényt. A beépített szabályzatdefiníciók bérlőszintű erőforrások. Egy beépített szabályzatdefiníció azonosítójának lekért értékéhez használja a [tenantResourceId()](template-functions-resource.md#tenantresourceid) függvényt.

Az alábbi példa bemutatja, hogyan [definiálhat](../../governance/policy/concepts/definition-structure.md) és rendelhet hozzá egy házirendet a felügyeleti csoport szintjén.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Üzembe helyezés előfizetésben és erőforráscsoportban

A felügyeleti csoportszintű telepítésből előfizetést célozhat meg a felügyeleti csoporton belül. Az alábbi példa létrehoz egy erőforráscsoportot egy előfizetésen belül, és üzembe helyez egy tárfiókot az adott erőforráscsoportban.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

* További információ a szerepkörök hozzárendelésről: [Azure-beli szerepkör-hozzárendelések hozzáadása Azure Resource Manager sablonokkal.](../../role-based-access-control/role-assignments-template.md)
* A munkaterület beállításainak központi telepítésére vonatkozó Azure Security Center lásd a [deployASCwithWorkspaceSettings.jsoldalon.](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)
* A sablonokat előfizetési és [bérlői szinten](deploy-to-subscription.md) [is üzembe helyezheti.](deploy-to-tenant.md)
