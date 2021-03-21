---
title: Sablon specifikációjának telepítése csatolt sablonként
description: Megtudhatja, hogyan helyezhet üzembe egy meglévő sablon-SPECT egy csatolt üzemelő példányban.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: b30a99a469ec010dcc2e128bbeb446b493631608
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518872"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Oktatóanyag: a sablon specifikációjának központi telepítése csatolt sablonként (előzetes verzió)

Megtudhatja, hogyan helyezhet üzembe egy meglévő [sablon-specifikációt](template-specs.md) egy [csatolt központi telepítés](linked-templates.md#linked-template)használatával. A sablon specifikációi segítségével megoszthatja az ARM-sablonokat a szervezet más felhasználóival. Miután létrehozta a sablon specifikációját, az Azure PowerShell vagy az Azure CLI használatával telepítheti a sablon specifikációját. A sablon specifikációját egy csatolt sablonnal is üzembe helyezheti a megoldás részeként.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> A sablonra vonatkozó specifikációk jelenleg előzetes verzióban érhetők el. Ha Azure PowerShell használatával szeretné használni, telepítenie kell a [5.0.0 vagy újabb verziót](/powershell/azure/install-az-ps). Ha az Azure CLI-vel szeretné használni, használja a [Version 2.14.2 vagy az újabb verziót](/cli/azure/install-azure-cli).

## <a name="create-a-template-spec"></a>Spec sablon létrehozása

Kövesse a rövid útmutató [: sablon létrehozása és üzembe helyezése specifikációt](quickstart-create-template-specs.md) a Storage-fiók telepítéséhez szükséges specifikáció létrehozásához. A következő szakaszban szüksége lesz a sablon spec, sablon spec neve és a sablon spec verziójának erőforráscsoport-nevére.

## <a name="create-the-main-template"></a>A fő sablon létrehozása

Ha egy ARM-sablonban szeretné üzembe helyezni a specifikációt, adjon hozzá egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonhoz. A `templateLink` tulajdonságban határozza meg a sablon erőforrás-azonosítóját. hozzon létre egy sablont a következő JSON-névvel **azuredeploy.json**. Ez az oktatóanyag feltételezi, hogy mentett egy elérési útra **c:\Templates\deployTS\azuredeploy.js** , de bármilyen elérési utat használhat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

A rendszer a függvény használatával hozza létre a sablon spec-AZONOSÍTÓját [`resourceID()`](template-functions-resource.md#resourceid) . Az resourceID () függvényben szereplő erőforráscsoport argumentum nem kötelező, ha a templateSpec az aktuális üzemelő példány ugyanazon erőforráscsoporthoz esik.  Közvetlenül is átadható az erőforrás-azonosító paraméterként. Az azonosító beszerzéséhez használja a következőt:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Ismert probléma a sablon spec AZONOSÍTÓjának beszerzése és a Windows PowerShellben lévő változóhoz rendelése.

---

A paraméterek sablonra való átadásának szintaxisa a következő:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> A apiVersion `Microsoft.Resources/deployments` 2020-06-01-es vagy újabb verziójúnak kell lennie.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A csatolt sablon központi telepítésekor a webalkalmazást és a Storage-fiókot is telepíti. A központi telepítés ugyanaz, mint a többi ARM-sablon üzembe helyezése.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Következő lépések

Ha szeretne többet megtudni arról, hogyan hozható létre csatolt sablonokat tartalmazó sablon-specifikáció, tekintse meg a [csatolt sablon specifikációjának létrehozása](template-specs-create-linked.md)című témakört.
