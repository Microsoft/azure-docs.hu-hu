---
title: Sablon létrehozása a csatolt sablonokkal
description: Útmutató sablon létrehozásához csatolt sablonokkal.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 31764b9adb1d0fc28a170fa2e6b5073812533083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889841"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Oktatóanyag: spec sablon létrehozása csatolt sablonokkal (előzetes verzió)

Megtudhatja, hogyan hozhat létre egy [sablon-SPECT](template-specs.md) egy fő sablonnal és egy [csatolt sablonnal](linked-templates.md#linked-template). A sablon specifikációi segítségével megoszthatja az ARM-sablonokat a szervezet más felhasználóival. Ebből a cikkből megtudhatja, hogyan hozhat létre egy sablon-specifikációt a fősablon és a hozzá társított sablonok a `relativePath` [telepítési erőforrás](/azure/templates/microsoft.resources/deployments)tulajdonságával történő előkészítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> A sablonra vonatkozó specifikációk jelenleg előzetes verzióban érhetők el. Ha Azure PowerShell használatával szeretné használni, telepítenie kell a [5.0.0 vagy újabb verziót](/powershell/azure/install-az-ps). Ha az Azure CLI-vel szeretné használni, használja a [Version 2.14.2 vagy az újabb verziót](/cli/azure/install-azure-cli).

## <a name="create-linked-templates"></a>Hivatkozott sablonok létrehozása

Hozza létre a fő sablont és a csatolt sablont.

Sablon csatolásához vegyen fel egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonba. A `templateLink` tulajdonságban a fölérendelt sablon elérési útjának megfelelően határozza meg a csatolt sablon relatív elérési útját.

A csatolt sablon neve **linkedTemplate.json**, és a fő sablon tárolási útvonalán található **összetevők nevű** almappában tárolódik.  A következő értékek egyikét használhatja a relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

A `relativePath` tulajdonság mindig ahhoz a sablonhoz viszonyítva van `relativePath` , ahol a be van jelentve, így ha van egy másik linkedTemplate2.js, amelyről a linkedTemplate.jsbe van kapcsolva, és a linkedTemplate2.jsa on ugyanazon összetevők almappában vannak tárolva, a linkedTemplate.json megadott relativePath csak `linkedTemplate2.json` .

1. Hozza létre a fő sablont a következő JSON-val. Mentse a fősablont **azuredeploy.jsa** helyi számítógépre. Ez az oktatóanyag feltételezi, hogy mentett egy elérési útra **c:\Templates\linkedTS\azuredeploy.js** , de bármilyen elérési utat használhat.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
          "apiVersion": "2020-10-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > A apiVersion `Microsoft.Resources/deployments` 2020-06-01-es vagy újabb verziójúnak kell lennie.

1. Hozzon létre egy **összetevők nevű könyvtárat** abban a mappában, amelyben a fő sablont mentette.
1. Hozza létre a csatolt sablont a következő JSON-val:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Mentse a sablont **linkedTemplate.jsként** az **összetevők mappában.**

## <a name="create-template-spec"></a>Sablon létrehozása – spec

A sablonok specifikációi az erőforráscsoportok szerint vannak tárolva.  Hozzon létre egy erőforráscsoportot, majd hozzon létre egy sablont a következő parancsfájllal. A sablon specifikációjának neve **Webspect**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "<path-to-main-template>"
```

---

Ha elkészült, megtekintheti a sablon specifikációját a Azure Portal vagy a következő parancsmag használatával:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

Most már üzembe helyezheti a sablon specifikációját. A sablon specifikációjának központi telepítése ugyanúgy történik, mint a benne található sablon üzembe helyezése, kivéve, ha a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját adja meg. Ugyanazokat az üzembe helyezési parancsokat használja, és szükség esetén adja át a paraméter értékét a sablon specifikációjának.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Ismert probléma a sablon spec AZONOSÍTÓjának beszerzése és a Windows PowerShellben lévő változóhoz rendelése.

---

## <a name="next-steps"></a>Következő lépések

Ha szeretne többet megtudni a sablon specifikációjának csatolt sablonként való üzembe helyezéséről, tekintse meg a következő [oktatóanyagot: a sablon specifikációjának telepítése csatolt sablonként](template-specs-deploy-linked-template.md).
