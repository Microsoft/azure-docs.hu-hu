---
title: Sablon létrehozása és üzembe helyezése – spec
description: Megtudhatja, hogyan hozhat létre egy sablont az ARM-sablon alapján. Ezután telepítse a SPECT egy erőforráscsoporthoz az előfizetésében.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 28987486726f5a88d20efe9fe8a766e536062c2c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889960"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Gyors útmutató: sablon létrehozása és üzembe helyezése specifikáció (előzetes verzió)

Ez a rövid útmutató bemutatja, hogyan csomagolhat egy Azure Resource Manager-sablont (ARM-sablont) a [sablon specba](template-specs.md). Ezután telepítse a sablon specifikációját. A sablon specifikációja egy ARM-sablont tartalmaz, amely egy Storage-fiókot telepít.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> A sablonra vonatkozó specifikációk jelenleg előzetes verzióban érhetők el. Ha Azure PowerShell használatával szeretné használni, telepítenie kell a [5.0.0 vagy újabb verziót](/powershell/azure/install-az-ps). Ha az Azure CLI-vel szeretné használni, használja a [Version 2.14.2 vagy az újabb verziót](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Sablon létrehozása

Helyi sablon alapján hozzon létre egy sablon-specifikációt. Másolja a következő sablont, és mentse helyileg egy **azuredeploy.js** nevű fájlba. Ez a rövid útmutató feltételezi, hogy mentett egy elérési útra **c:\Templates\azuredeploy.js** , de bármilyen elérési utat használhat.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Sablon létrehozása – spec

A sablon specifikációja egy nevű erőforrástípus `Microsoft.Resources/templateSpecs` . A sablon specifikációjának létrehozásához használja a PowerShellt, az Azure CLI-t, a portált vagy egy ARM-sablont.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy új erőforráscsoportot, amely tartalmazza a sablon specifikációját.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Hozza létre a sablon specifikációját az erőforráscsoporthoz. Adja az új sablonnak a **storageSpec** nevet.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

1. Hozzon létre egy új erőforráscsoportot, amely tartalmazza a sablon specifikációját.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Hozza létre a sablon specifikációját az erőforráscsoporthoz. Adja az új sablonnak a **storageSpec** nevet.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **sablonhoz tartozó specifikációkat**. Válassza a **sablonhoz tartozó specifikációk** lehetőséget az elérhető lehetőségek közül.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="keresési sablon specifikációi":::

1. Válassza a **sablon importálása** lehetőséget.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="sablon importálása":::

1. Válassza ki a mappa ikont.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="mappa megnyitása":::

1. Navigáljon a mentett helyi sablonhoz, és válassza ki. Válassza a **Megnyitás** lehetőséget.
1. Válassza az **Importálás** lehetőséget.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="importálási beállítás kiválasztása":::

1. Adja meg a következő értékeket:

    - **Név**: adja meg a sablon specifikációjának nevét.  Például: **storageSpec**
    - **Előfizetés**: válassza ki a sablon specifikációjának létrehozásához használt Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, majd adjon meg egy új erőforráscsoport-nevet.  Például: **templateSpecRG**.
    - **Hely**: válasszon egy helyet az erőforráscsoportnak. Például:  **USA 2. nyugati** régiója.
    - **Verzió**: adja meg a sablon specifikációjának verzióját. használja a **1,0**-es verziót.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

# <a name="arm-template"></a>[ARM-sablon](#tab/azure-resource-manager)

> [!NOTE]
> ARM-sablon használata helyett a PowerShell vagy a CLI használatával kell létrehoznia a sablon specifikációját. Ezek az eszközök automatikusan konvertálják a csatolt sablonokat a fő sablonhoz csatlakoztatott összetevőkre. Amikor ARM-sablonnal hozza létre a sablon specifikációját, manuálisan kell hozzáadnia ezeket a csatolt sablonokat összetevőkként, ami bonyolult lehet.

1. Amikor ARM-sablont használ a sablon specifikációjának létrehozásához, a sablon be van ágyazva az erőforrás-definícióba. Néhány módosítást kell végeznie a helyi sablonban. Másolja a következő sablont, és mentse helyileg **azuredeploy.jsként**.

    > [!NOTE]
    > A beágyazott sablonban az összes [sablon kifejezését](template-expressions.md) egy második bal oldali szögletes zárójeltel kell megszökni. `"[[`A helyett használja `"[` . A JSON-tömbök továbbra is egyetlen bal oldali szögletes zárójelet használnak.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Hozzon létre egy új erőforráscsoportot az Azure CLI vagy a PowerShell használatával.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. A sablon üzembe helyezése az Azure CLI-vel vagy a PowerShell-lel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

A sablon specifikációjának telepítéséhez ugyanazokat a telepítési parancsokat használja, mint amelyeket a sablon üzembe helyezéséhez használ. Adja át az üzembe helyezni kívánt sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy erőforráscsoportot, amely tartalmazza az új Storage-fiókot.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Szerezze be a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Telepítse a sablon specifikációját.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. A paramétereket pontosan úgy adja meg, mint egy ARM-sablonhoz. Telepítse újra a sablon specifikációját a Storage-fiók típusának megfelelő paraméterrel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

1. Hozzon létre egy erőforráscsoportot, amely tartalmazza az új Storage-fiókot.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Szerezze be a sablonhoz tartozó specifikáció erőforrás-AZONOSÍTÓját.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Ismert probléma a sablon spec AZONOSÍTÓjának beszerzése és a Windows PowerShellben lévő változóhoz rendelése.

1. Telepítse a sablon specifikációját.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. A paramétereket pontosan úgy adja meg, mint egy ARM-sablonhoz. Telepítse újra a sablon specifikációját a Storage-fiók típusának megfelelő paraméterrel.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki a létrehozott specifikációt.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="sablon specifikációinak kiválasztása":::

1. Válassza az **Üzembe helyezés** lehetőséget.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="sablon üzembe helyezése – specifikációk":::

1. Adja meg a következő értékeket:

    - **Előfizetés**: válasszon ki egy Azure-előfizetést az erőforrás létrehozásához.
    - **Erőforráscsoport**: válassza az **új létrehozása** elemet, majd adja meg a **storageRG**.
    - **Storage-fiók típusa**: válassza a **Standard_GRS** lehetőséget.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

# <a name="arm-template"></a>[ARM-sablon](#tab/azure-resource-manager)

1. Másolja a következő sablont, és mentse helyileg egy **storage.js** nevű fájlba.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Hozzon létre egy új erőforráscsoportot a Storage-fiókhoz az Azure CLI vagy a PowerShell használatával.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. A sablon üzembe helyezése az Azure CLI-vel vagy a PowerShell-lel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Hozzáférés biztosítása

Ha szeretné, hogy a szervezet többi felhasználója is telepítse a sablon specifikációját, olvasási hozzáférést kell adnia nekik. Az olvasó szerepkört hozzárendelheti egy Azure AD-csoporthoz azon erőforráscsoport esetében, amely a megosztani kívánt sablon-specifikációkat tartalmazza. További információ: [oktatóanyag: csoport hozzáférésének biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="update-template"></a>Sablon frissítése

Tegyük fel, hogy azonosította a sablonban használni kívánt változást a sablon specifikációjában. A következő sablon hasonlít a korábbi sablonhoz, csak a Storage-fiók nevének előtagját adja hozzá. Másolja a következő sablont, és frissítse a azuredeploy.jsfájlt.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>A sablon specifikációjának frissítése

Ahelyett, hogy a módosított sablonhoz új sablont hozna létre, adjon hozzá egy nevű új verziót `2.0` a meglévő sablon specifikációhoz. a felhasználók a telepítendő verziót is választhatják.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy új verziót a sablon spec.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Az új verzió üzembe helyezéséhez szerezze be a verzió erőforrás-AZONOSÍTÓját `2.0` .

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. A verzió üzembe helyezése. Adja meg a Storage-fiók nevének előtagját.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

1. Hozzon létre egy új verziót a sablon spec.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Az új verzió üzembe helyezéséhez szerezze be a verzió erőforrás-AZONOSÍTÓját `2.0` .

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. A verzió üzembe helyezése. Adja meg a Storage-fiók nevének előtagját.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A sablon specifikációjában válassza az **új verzió létrehozása** lehetőséget.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="új verzió létrehozása":::

1. Nevezze el az új verziót `2.0` , és opcionálisan vegyen fel megjegyzéseket. Válassza a **Sablon szerkesztése** lehetőséget.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="új verzió neve":::

1. Cserélje le a sablon tartalmát a frissített sablonnal. Válassza a **felülvizsgálat + mentés** lehetőséget.
1. Válassza a **Módosítások mentése** lehetőséget.

1. Az új verzió üzembe helyezéséhez válassza a **verziók** lehetőséget.

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="verziók listázása":::

1. A telepíteni kívánt verzióhoz válassza ki a három pontot, és **telepítse** a programot.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="telepítendő verzió kiválasztása":::

1. Töltse ki a mezőket a korábbi verzió telepítésekor.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

# <a name="arm-template"></a>[ARM-sablon](#tab/azure-resource-manager)

1. A sablonhoz tartozó specifikációk használatával a helyi sablonban is módosításokat kell végeznie. Másolja a következő sablont, és mentse helyileg azuredeploy.jsként.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Ha az új verziót hozzá szeretné adni a sablonhoz, telepítse a sablont az Azure CLI-vel vagy a PowerShell-lel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. Másolja a következő sablont, és mentse helyileg egy **storage.js** nevű fájlba.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. A sablon üzembe helyezése az Azure CLI-vel vagy a PowerShell-lel.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban üzembe helyezett erőforrás törléséhez törölje mindkét létrehozott erőforráscsoportot.

1. Az Azure Portalon válassza az Erőforráscsoport lehetőséget a bal oldali menüben.

1. Adja meg az erőforráscsoport nevét (templateSpecRG és storageRG) a szűrés név szerint mezőben.

1. Válassza ki az erőforráscsoport nevét.

1. A felső menüben válassza az Erőforráscsoport törlése lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha szeretne többet megtudni arról, hogyan hozható létre csatolt sablonokat tartalmazó sablon-specifikáció, tekintse meg a [csatolt sablon specifikációjának létrehozása](template-specs-create-linked.md)című témakört.
