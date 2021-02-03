---
title: Erőforrások üzembe helyezése az előfizetésben
description: Leírja, hogyan lehet erőforráscsoportot létrehozni egy Azure Resource Manager sablonban. Azt is bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetési hatókörben.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: b5c99e5dc21c2b93f1c9da3977302a2dd311277f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491503"
---
# <a name="subscription-deployments-with-arm-templates"></a>Előfizetés üzembe helyezése ARM-sablonokkal

Az erőforrások kezelésének egyszerűbbé tételéhez Azure Resource Manager sablonnal (ARM-sablon) használhatja az erőforrásokat az Azure-előfizetése szintjén. Telepítheti például a [szabályzatokat](../../governance/policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) az előfizetésre, amely az előfizetésen belül érvényes. Az előfizetéshez tartozó erőforráscsoportokat is létrehozhat, és erőforrásokat helyezhet üzembe az előfizetésben lévő erőforráscsoportok számára.

> [!NOTE]
> Az előfizetések szintjén üzembe helyezhetők 800 különböző erőforráscsoportok.

A sablonok előfizetési szinten történő üzembe helyezéséhez használja az Azure CLI, a PowerShell, a REST API vagy a portált.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípus lehet központilag telepíteni az előfizetési szintre. Ez a szakasz felsorolja, hogy milyen típusú erőforrástípusok támogatottak.

Az Azure-tervezetek esetében használja az alábbiakat:

* [leletek](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [tervrajzok](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [verziók (tervrajzok)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure-szabályzatok esetén használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szervizelések](/azure/templates/microsoft.policyinsights/remediations)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata esetén használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Az erőforráscsoportok számára központilag telepített beágyazott sablonok esetén használja a következőt:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)

Új erőforráscsoportok létrehozásához használja a következőt:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Az előfizetés kezeléséhez használja a következőt:

* [Advisor-konfigurációk](/azure/templates/microsoft.advisor/configurations)
* [költségvetése](/azure/templates/microsoft.consumption/budgets)
* [Elemzési profil módosítása](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Címkék](/azure/templates/microsoft.resources/tags)

Más támogatott típusok a következők:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Séma

Az előfizetési szintű központi telepítések sémája eltér az erőforráscsoport-telepítésekhez használt sémától.

Sablonok esetén használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

Az előfizetésre való központi telepítéshez használja az előfizetési szintű központi telepítési parancsokat.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetén használja az [az Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create). A következő példa egy sablont helyez üzembe egy erőforráscsoport létrehozásához:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell üzembe helyezési parancsához használja a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) vagy annak aliasát `New-AzSubscriptionDeployment` . A következő példa egy sablont helyez üzembe egy erőforráscsoport létrehozásához:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
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

Az előfizetési szintű központi telepítések esetében meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat. A [felügyeleti csoportnak](deploy-to-management-group.md) és a [bérlői](deploy-to-tenant.md) példányoknak is helyet kell megadniuk. Az [erőforráscsoport](deploy-to-resource-group.md) -telepítések esetében az erőforráscsoport helye a központi telepítési adattárolási szolgáltatás tárolására szolgál.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy _azuredeploy.js_ nevű sablon üzembe helyezése például a **azuredeploy** alapértelmezett központi telepítési nevét hozza létre.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha például létrehoz egy **deployment1** nevű előfizetést a **CentralUS**-ben, akkor később nem hozhat létre újabb telepítést a **deployment1** névvel, de a **westus** helyét. Ha a hibakódot kapja `InvalidDeploymentLocation` , használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Az előfizetések telepítésekor a következő erőforrások helyezhetők üzembe:

* a művelethez tartozó cél-előfizetés
* bármely előfizetés a bérlőn
* az előfizetésben vagy más előfizetésekben található erőforráscsoportok
* az előfizetés bérlője

A [kiterjesztési erőforrás](scope-extension-resources.md) hatóköre olyan cél lehet, amely eltér a telepítési céltól.

A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Ez a szakasz bemutatja, hogyan határozhat meg különböző hatóköröket. Ezeket a különböző hatóköröket egyetlen sablonban kombinálhatja.

### <a name="scope-to-target-subscription"></a>Hatókör a cél előfizetéshez

Ha erőforrásokat szeretne telepíteni a cél előfizetésre, adja hozzá ezeket az erőforrásokat a sablon erőforrások szakaszához.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Az előfizetésre való központi telepítésre vonatkozó Példákért lásd: [erőforráscsoportok létrehozása](#create-resource-groups) és [szabályzat-definíció kiosztása](#assign-policy-definition).

### <a name="scope-to-other-subscription"></a>Hatókör más előfizetésre

Ha olyan előfizetéshez szeretne erőforrásokat telepíteni, amely eltér a művelet előfizetéstől, adjon hozzá egy beágyazott központi telepítést. Állítsa be annak az `subscriptionId` előfizetésnek az azonosítóját, amelyre telepíteni kívánja a tulajdonságot. Állítsa be a beágyazott üzemelő `location` példány tulajdonságát.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Hatókör az erőforráscsoporthoz

Ha erőforrásokat szeretne telepíteni az előfizetésben lévő erőforráscsoporthoz, adjon hozzá egy beágyazott központi telepítést, és vegye fel a `resourceGroup` tulajdonságot. A következő példában a beágyazott telepítés a nevű erőforráscsoportot célozza meg `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Az erőforráscsoporthoz való központi telepítésre példát az [erőforráscsoport és erőforrások létrehozása](#create-resource-group-and-resources)című témakörben talál.

### <a name="scope-to-tenant"></a>Hatókör a bérlőre

Ahhoz, hogy erőforrásokat hozzon létre a bérlőn, állítsa be a következőt: `scope` `/` . A sablont telepítő felhasználónak rendelkeznie kell a [bérlőn való üzembe helyezéshez szükséges hozzáféréssel](deploy-to-tenant.md#required-access).

Beágyazott központi telepítés használatához állítsa be a következőt: `scope` és `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

A hatókört `/` bizonyos erőforrástípusok, például a felügyeleti csoportok esetében is beállíthatja.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

További információ: [felügyeleti csoport](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Erőforráscsoportok

### <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

Ha egy ARM-sablonban szeretne erőforráscsoportot létrehozni, Definiáljon egy [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) -erőforrást az erőforráscsoport nevével és helyével.

A következő sablon egy üres erőforráscsoportot hoz létre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Több erőforráscsoport létrehozásához használja a [Másolás elemet](copy-resources.md) az erőforráscsoportok használatával.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Az erőforrás-iterációval kapcsolatos információkért lásd: [erőforrás-iteráció az ARM-sablonokban](./copy-resources.md)és [oktatóanyag: több erőforrás-példány létrehozása ARM-sablonokkal](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Erőforráscsoport és erőforrások létrehozása

Az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez használjon egy beágyazott sablont. A beágyazott sablon meghatározza az erőforráscsoporthoz telepítendő erőforrásokat. Állítsa be a beágyazott sablont az erőforráscsoport függőként, hogy az erőforrás-csoport az erőforrások telepítése előtt is elérhető legyen. Akár 800 erőforráscsoporthoz is üzembe helyezhető.

A következő példában létrehozunk egy erőforráscsoportot, és üzembe helyezünk egy Storage-fiókot az erőforráscsoporthoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Szabályzat-definíció megadása

Az alábbi példa egy meglévő szabályzat-definíciót rendel hozzá az előfizetéshez. Ha a házirend-definíció paraméterekkel rendelkezik, adja meg őket objektumként. Ha a házirend-definíció nem fogad paramétereket, használja az alapértelmezett üres objektumot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

A sablon Azure CLI-vel történő üzembe helyezéséhez használja a következőt:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Szabályzat-definíciók létrehozása és kiosztása

[Megadhatja és hozzárendelhet](../../governance/policy/concepts/definition-structure.md) egy szabályzat-definíciót ugyanabban a sablonban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Az előfizetéshez tartozó szabályzat-definíció létrehozásához és az előfizetéshez való hozzárendeléséhez használja az alábbi CLI-parancsot:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Terv definíciójának létrehozása

[Létrehozhat](../../governance/blueprints/tutorials/create-from-sample.md) egy tervrajz-definíciót egy sablonból.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Az előfizetéshez tartozó terv definíciójának létrehozásához használja az alábbi CLI-parancsot:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

A sablon PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Hozzáférés-vezérlés

A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md).

Az alábbi példa létrehoz egy erőforráscsoportot, egy zárolást alkalmaz rá, és hozzárendel egy szerepkört egy rendszerbiztonsági tag számára.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

* A Azure Security Center munkaterület-beállításainak központi telepítésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* A sablonok a [githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)találhatók.
* A sablonokat [felügyeleti csoport szintjén](deploy-to-management-group.md) és [bérlői szinten](deploy-to-tenant.md)is üzembe helyezheti.
