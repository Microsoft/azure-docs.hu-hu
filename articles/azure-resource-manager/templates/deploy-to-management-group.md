---
title: Erőforrások központi telepítése a felügyeleti csoportba
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a felügyeleti csoport hatókörében egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: a203dd2c52bdc889452a6755fb025c7ed5721a59
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491623"
---
# <a name="management-group-deployments-with-arm-templates"></a>Felügyeleti csoportok üzembe helyezése ARM-sablonokkal

Amikor a szervezete leállt, üzembe helyezhet egy Azure Resource Manager sablont (ARM-sablon) az erőforrások létrehozásához a felügyeleti csoport szintjén. Előfordulhat például, hogy meg kell adnia és hozzá kell rendelnie egy felügyeleti csoport [szabályzatait](../../governance/policy/overview.md) vagy az [Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC)](../../role-based-access-control/overview.md) . A felügyeleti csoport szintű sablonok használatával a felügyeleti csoport szintjén deklarálhatja a házirendeket, és rendelhet hozzá szerepköröket.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípust lehet központilag telepíteni a felügyeleti csoport szintjére. Ez a szakasz felsorolja, hogy milyen típusú erőforrástípusok támogatottak.

Az Azure-tervezetek esetében használja az alábbiakat:

* [leletek](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [tervrajzok](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [verziók](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure-szabályzatok esetén használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szervizelések](/azure/templates/microsoft.policyinsights/remediations)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata esetén használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Az előfizetések vagy erőforráscsoportok számára üzembe helyezett beágyazott sablonok esetében használja a következőt:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)

Az erőforrások kezeléséhez használja a következőt:

* [Címkék](/azure/templates/microsoft.resources/tags)

A felügyeleti csoportok bérlői szintű erőforrások. Felügyeleti csoportokat azonban létrehozhat egy felügyeleti csoport központi telepítésében, ha az új felügyeleti csoport hatókörét a bérlőre állítja be. Lásd: [felügyeleti csoport](#management-group).

## <a name="schema"></a>Séma

A felügyeleti csoportok központi telepítéséhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

Felügyeleti csoportba való központi telepítéshez használja a felügyeleti csoport telepítési parancsait.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetében használja az [az Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell esetén használja a [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

További információt az üzembe helyezési parancsokról és az ARM-sablonok üzembe helyezési lehetőségeiről a következő témakörben talál:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Sablonok üzembe helyezése a GitHub-tárházból a központi telepítés gomb használatával](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Felügyeleti csoport szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat. Az [előfizetés](deploy-to-subscription.md) és a [bérlő](deploy-to-tenant.md) üzembe helyezéséhez is szükség van egy helyre. Az [erőforráscsoport](deploy-to-resource-group.md) -telepítések esetében az erőforráscsoport helye a központi telepítési adattárolási szolgáltatás tárolására szolgál.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy _azuredeploy.js_ nevű sablon üzembe helyezése például a **azuredeploy** alapértelmezett központi telepítési nevét hozza létre.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha például létrehoz egy felügyeleti csoport központi telepítését a **CentralUS** nevű **deployment1** , akkor később nem hozhat létre újabb telepítést a **deployment1** névvel, de a **westus** helyét. Ha a hibakódot kapja `InvalidDeploymentLocation` , használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Felügyeleti csoportba való központi telepítés esetén az erőforrások a következőre helyezhetők:

* a cél felügyeleti csoport a műveletből
* egy másik felügyeleti csoport a bérlőben
* a felügyeleti csoportban lévő előfizetések
* erőforráscsoportok a felügyeleti csoportban
* az erőforráscsoport bérlője

A [kiterjesztési erőforrás](scope-extension-resources.md) hatóköre olyan cél lehet, amely eltér a telepítési céltól.

A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Ez a szakasz bemutatja, hogyan határozhat meg különböző hatóköröket. Ezeket a különböző hatóköröket egyetlen sablonban kombinálhatja.

### <a name="scope-to-target-management-group"></a>Hatókör a cél felügyeleti csoportnak

A sablon erőforrások szakaszában meghatározott erőforrások a központi telepítési paranccsal lesznek alkalmazva a felügyeleti csoportra.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Hatókör egy másik felügyeleti csoportra

Egy másik felügyeleti csoport megcélzásához adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot. Állítsa a `scope` tulajdonságot értékre a következő formátumban: `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Hatókör az előfizetéshez

Az előfizetéseket egy felügyeleti csoporton belül is megcélozhatja. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

A felügyeleti csoporton belüli előfizetés megcélzásához használjon beágyazott központi telepítést és a `subscriptionId` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Hatókör az erőforráscsoporthoz

Az erőforráscsoportok a felügyeleti csoporton belül is megadhatók. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Egy erőforráscsoport a felügyeleti csoporton belüli célzásához használjon egy beágyazott telepítést. Adja meg a `subscriptionId` és a `resourceGroup` tulajdonságokat. Ne állítson be helyet a beágyazott központi telepítéshez, mert az az erőforráscsoport helyén van üzembe helyezve.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Ha egy felügyeleti csoport központi telepítését szeretné használni az előfizetésen belüli erőforráscsoport létrehozásához és a Storage-fiók üzembe helyezéséhez az adott erőforráscsoporthoz, tekintse meg az [előfizetés és az erőforráscsoport üzembe helyezése](#deploy-to-subscription-and-resource-group)című témakört.

### <a name="scope-to-tenant"></a>Hatókör a bérlőre

Ahhoz, hogy erőforrásokat hozzon létre a bérlőn, állítsa be a következőt: `scope` `/` . A sablont telepítő felhasználónak rendelkeznie kell a [bérlőn való üzembe helyezéshez szükséges hozzáféréssel](deploy-to-tenant.md#required-access).

Beágyazott központi telepítés használatához állítsa be a következőt: `scope` és `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

A hatókört `/` bizonyos erőforrástípusok, például a felügyeleti csoportok esetében is beállíthatja. Az új felügyeleti csoport létrehozásáról a következő szakaszban olvashat.

## <a name="management-group"></a>Felügyeleti csoport

Ha felügyeleti csoportot szeretne létrehozni egy felügyeleti csoport központi telepítésében, a hatókört a `/` felügyeleti csoportra vonatkozóan kell beállítania.

A következő példa egy új felügyeleti csoportot hoz létre a gyökérszintű felügyeleti csoportban.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

A következő példa létrehoz egy új felügyeleti csoportot a szülőként megadott felügyeleti csoportban. Figyelje meg, hogy a hatókör a következőre van beállítva: `/` .

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

## <a name="azure-policy"></a>Azure Policy

A felügyeleti csoportba központilag telepített egyéni házirend-definíciók a felügyeleti csoport bővítményei. Egyéni szabályzat-definíció AZONOSÍTÓjának lekéréséhez használja a [extensionResourceId ()](template-functions-resource.md#extensionresourceid) függvényt. A beépített szabályzat-definíciók a bérlői szintű erőforrások. A beépített szabályzat-definíciók AZONOSÍTÓjának lekéréséhez használja a [tenantResourceId ()](template-functions-resource.md#tenantresourceid) függvényt.

Az alábbi példa bemutatja, hogyan [határozhat meg](../../governance/policy/concepts/definition-structure.md) egy házirendet a felügyeleti csoport szintjén, és hogyan rendelheti hozzá.

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

## <a name="deploy-to-subscription-and-resource-group"></a>Üzembe helyezés az előfizetésben és az erőforráscsoporthoz

A felügyeleti csoport szintjén üzemelő példányok esetében a felügyeleti csoporton belül is megcélozhat egy előfizetést. A következő példa létrehoz egy erőforráscsoportot egy előfizetésen belül, és üzembe helyez egy Storage-fiókot az adott erőforráscsoporthoz.

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
            "apiVersion": "2020-06-01",
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
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
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

* A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak központi telepítésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) és [bérlői szinten](deploy-to-tenant.md)is üzembe helyezheti.
