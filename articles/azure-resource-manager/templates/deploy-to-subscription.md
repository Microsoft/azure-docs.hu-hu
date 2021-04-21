---
title: Erőforrások üzembe helyezése az előfizetésben
description: Ismerteti, hogyan hozhat létre erőforráscsoportot egy Azure Resource Manager sablonban. Azt is bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetés hatókörében.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 3598fe290fd993cbbc662ba9d3a3c5ba8c207bc0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781918"
---
# <a name="subscription-deployments-with-arm-templates"></a>Előfizetések üzembe helyezése ARM-sablonokkal

Az erőforrások felügyeletének egyszerűsítése érdekében egy Azure Resource Manager sablonnal (ARM-sablon) helyezhet üzembe erőforrásokat az Azure-előfizetés szintjén. Üzembe helyezhet például [](../../governance/policy/overview.md) szabályzatokat és [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) az előfizetésében, amely alkalmazza őket az előfizetésben. Az előfizetésen belül erőforráscsoportokat is létrehozhat, és erőforrásokat helyezhet üzembe az előfizetés erőforráscsoportjaiban.

> [!NOTE]
> Előfizetési szintű üzemelő példányban 800 különböző erőforráscsoportban helyezhet üzembe.

Sablonok előfizetési szinten való üzembe helyezéséhez használja az Azure CLI-t, a PowerShellt, REST API portált.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípus helyezhető üzembe az előfizetés szintjén. Ez a szakasz a támogatott erőforrástípusokat sorolja fel.

A Azure Blueprints használja a következőt:

* [Leletek](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Tervrajz](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure-szabályzatok:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions (szabályzatdefiníciók)](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szervizelések](/azure/templates/microsoft.policyinsights/remediations)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatáshoz használja a következőt:

* [roleAssignments (szerepkör hozzárendelései)](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions (szerepkördefiníciók)](/azure/templates/microsoft.authorization/roledefinitions)

Az erőforráscsoportokban üzembe helyezett beágyazott sablonok esetében használja a következőt:

* [Telepítések](/azure/templates/microsoft.resources/deployments)

Új erőforráscsoportok létrehozásához használja a következőt:

* [resourceGroups (erőforráscsoportok)](/azure/templates/microsoft.resources/resourcegroups)

Az előfizetés kezeléséhez használja a következőt:

* [Advisor-konfigurációk](/azure/templates/microsoft.advisor/configurations)
* [Költségvetések](/azure/templates/microsoft.consumption/budgets)
* [változáselemzés profil](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Címkék](/azure/templates/microsoft.resources/tags)

További támogatott típusok:

* [scopeAssignments (hatókör hozzárendelései)](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions (eventSubscriptions)](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Séma

Az előfizetés-szintű üzembe helyezések sémája eltér az erőforráscsoportok üzembe helyezéséhez használt sémától.

Sablonokhoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

Az előfizetésben való üzembe helyezéshez használja az előfizetés-szintű üzembe helyezési parancsokat.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-hez használja [az az deployment sub create szolgáltatásokat.](/cli/azure/deployment/sub#az_deployment_sub_create) Az alábbi példa üzembe helyez egy sablont egy erőforráscsoport létrehozásához:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell üzembe helyezési parancshoz használja a [New-AzDeployment parancsot](/powershell/module/az.resources/new-azdeployment) vagy annak aliasát. `New-AzSubscriptionDeployment` Az alábbi példa üzembe helyez egy sablont egy erőforráscsoport létrehozásához:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Az üzembe helyezési parancsokkal és az ARM-sablonok üzembe helyezésének beállításaival kapcsolatos további információkért lásd:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-val](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Üzembe helyezési gomb használata sablonok GitHub-adattárból való üzembe helyezéséhez](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Üzembe helyezés helye és neve

Az előfizetési szintű üzemelő példányok esetén meg kell adnia az üzemelő példány helyét. Az üzembe helyezés helye elkülönül az üzembe helyezett erőforrások helyétől. Az üzembe helyezési hely határozza meg, hogy hol tárolja az üzembe helyezési adatokat. [A felügyeleti csoportok](deploy-to-management-group.md) és [bérlők](deploy-to-tenant.md) üzembe helyezéséhez hely is szükséges. Az [erőforráscsoportok üzemelő](deploy-to-resource-group.md) példányai esetén az erőforráscsoport helye az üzembe helyezési adatok tárolására használatos.

Meg kell adnia a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet. Az alapértelmezett név a sablonfájl neve. Ha például üzembe helyez egyazuredeploy.js _nevű sablont,_ létrejön egy **azuredeploy nevű alapértelmezett üzemelő példánynév.**

A hely minden üzembe helyezési név esetén nem módosítható. Nem hozhat létre üzemelő példányokat egy helyen, ha már létezik azonos nevű üzemelő példány egy másik helyen. Ha például a **centralusban** létrehoz egy **deployment1** nevű előfizetés-üzemelő példányt, később nem hozhat létre másik üzemelő példányt **deployment1** néven, csak a **westus** helyen. Ha a hibakód jelenik meg, vagy használjon egy másik nevet, vagy ugyanazt a helyet, mint az előző üzemelő `InvalidDeploymentLocation` példány a névhez.

## <a name="deployment-scopes"></a>Üzembe helyezési hatókörök

Előfizetésben való üzembe helyezéskor a következő erőforrásokat helyezheti üzembe:

* a művelet cél-előfizetése
* bármely előfizetés a bérlőben
* erőforráscsoportok az előfizetésen vagy más előfizetésen belül
* az előfizetés bérlője

A [bővítményerőforrás](scope-extension-resources.md) hatóköre az üzembe helyezési céltól eltérő célra is kiterjedhet.

A sablont üzembe helyező felhasználónak hozzáféréssel kell lennie a megadott hatókörben.

Ez a szakasz a különböző hatókörök megadását mutatja be. Ezeket a különböző hatókörök egyetlen sablonban kombinálhatóak.

### <a name="scope-to-target-subscription"></a>Hatókör cél-előfizetéshez

Ha erőforrásokat szeretne üzembe helyezni a cél-előfizetésben, adja hozzá ezeket az erőforrásokat a sablon resources (erőforrások) szakaszához.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Példák az előfizetésben való üzembe helyezésre: [Erőforráscsoportok létrehozása](#create-resource-groups) és [Szabályzatdefiníció hozzárendelése.](#assign-policy-definition)

### <a name="scope-to-other-subscription"></a>Hatókör másik előfizetésre

Ha a művelettől eltérő előfizetésben szeretne erőforrásokat üzembe helyezni, adjon hozzá egy beágyazott üzemelő példányt. Állítsa `subscriptionId` a tulajdonságot annak az előfizetésnek az azonosítójára, amelybe az üzembe helyezést szeretné helyezni. Állítsa be `location` a beágyazott üzemelő példány tulajdonságát.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Hatókör erőforráscsoportra

Ha erőforrásokat szeretne üzembe helyezni egy erőforráscsoportban az előfizetésen belül, adjon hozzá egy beágyazott üzemelő példányot, és adja hozzá a `resourceGroup` tulajdonságot. A következő példában a beágyazott üzemelő példány egy nevű erőforráscsoportot célozza `demoResourceGroup` meg.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Egy erőforráscsoportban való üzembe helyezésre vonatkozó példáért lásd: [Erőforráscsoport és erőforrások létrehozása.](#create-resource-group-and-resources)

### <a name="scope-to-tenant"></a>Hatókör bérlőre

Ha erőforrásokat hoz létre a bérlőn, állítsa a `scope` következőt: `/` . A sablont üzembe helyező felhasználónak a bérlőn való üzembe helyezéshez szükséges [hozzáféréssel kell lennie.](deploy-to-tenant.md#required-access)

Beágyazott üzembe helyezéshez állítsa be a és a `scope` `location` halmazt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

A hatókört egyes erőforrástípusokra, például felügyeleti csoportokra `/` is beállíthatja.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

További információ: [Felügyeleti csoport.](deploy-to-management-group.md#management-group)

## <a name="resource-groups"></a>Erőforráscsoportok

### <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

Erőforráscsoport ARM-sablonban való létrehozásához definiálja a [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) erőforrást az erőforráscsoport nevével és helyével.

Az alábbi sablon egy üres erőforráscsoportot hoz létre.

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
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Használja a [másolás elemet](copy-resources.md) erőforráscsoportokkal több erőforráscsoport létrehozásához.

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
      "apiVersion": "2020-10-01",
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

További információ az erőforrás-iterációról: [Erőforrás-iteráció AZ ARM-sablonokban](./copy-resources.md)és Oktatóanyag: Több erőforráspéldány létrehozása [ARM-sablonokkal.](./template-tutorial-create-multiple-instances.md)

### <a name="create-resource-group-and-resources"></a>Erőforráscsoport és erőforrások létrehozása

Az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez használjon beágyazott sablont. A beágyazott sablon határozza meg az erőforráscsoportban üzembe helyezni kívánt erőforrásokat. Állítsa be a beágyazott sablont az erőforráscsoporttól függőként, hogy az erőforrások üzembe helyezése előtt ellenőrizze, hogy létezik-e az erőforráscsoport. Legfeljebb 800 erőforráscsoportot helyezhet üzembe.

Az alábbi példa létrehoz egy erőforráscsoportot, és üzembe helyez egy tárfiókot az erőforráscsoportban.

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
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
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

### <a name="assign-policy-definition"></a>Szabályzatdefiníció hozzárendelése

Az alábbi példa egy meglévő szabályzatdefiníciót rendel hozzá az előfizetéshez. Ha a szabályzatdefiníció paramétereket használ, adja meg őket objektumként. Ha a szabályzatdefiníció nem használ paramétereket, használja az alapértelmezett üres objektumot.

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

A sablon Azure CLI-val való üzembe helyezéséhez használja a következőt:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

A sablon PowerShell-rel való üzembe helyezéséhez használja a következőt:

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

### <a name="create-and-assign-policy-definitions"></a>Szabályzatdefiníciók létrehozása és hozzárendelése

Ugyanabban a [sablonban](../../governance/policy/concepts/definition-structure.md) definiálhat és rendelhet hozzá szabályzatdefiníciót.

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

A következő CLI-paranccsal hozhatja létre a szabályzatdefiníciót az előfizetésben, és hozzárendelheti az előfizetéshez:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

A sablon PowerShell-rel való üzembe helyezéséhez használja a következőt:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Tervdefiníció létrehozása

Sablonból [is](../../governance/blueprints/tutorials/create-from-sample.md) létrehozhat tervdefiníciót.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

A tervdefiníció előfizetésben való létrehozásához használja a következő CLI-parancsot:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

A sablon PowerShell-rel való üzembe helyezéséhez használja a következőt:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Hozzáférés-vezérlés

További információ a szerepkörök hozzárendelésről: [Azure-beli szerepkör-hozzárendelések hozzáadása Azure Resource Manager sablonokkal.](../../role-based-access-control/role-assignments-template.md)

Az alábbi példa létrehoz egy erőforráscsoportot, zárolást alkalmaz rá, és hozzárendel egy szerepkört egy rendszerbiztonsági taghoz.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

* A munkaterület beállításainak központi telepítésére vonatkozó Azure Security Center lásd a [deployASCwithWorkspaceSettings.jsoldalon.](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)
* Mintasablonok a [GitHubon találhatók.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)
* A sablonokat felügyeleti csoport [és](deploy-to-management-group.md) bérlői szinten is [üzembe helyezheti.](deploy-to-tenant.md)
