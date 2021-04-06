---
title: Sablonok üzembe helyezése Cloud Shell
description: Erőforrások üzembe helyezése az Azure-ban Azure Resource Manager és Azure Cloud Shell használatával. Az erőforrások Azure Resource Manager sablonban (ARM-sablon) vannak definiálva.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028607"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>ARM-sablonok üzembe helyezése Azure Cloud Shell

Azure Resource Manager-sablon (ARM-sablon) üzembe helyezéséhez [Azure Cloud shellt](../../cloud-shell/overview.md) használhat. Telepítheti a távolról tárolt ARM-sablonokat, illetve a helyi Storage-fiókban tárolt ARM-sablonokat Cloud Shell számára.

Bármely hatókörbe üzembe helyezhető. Ez a cikk egy erőforráscsoport üzembe helyezését mutatja be.

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

Külső sablon üzembe helyezéséhez pontosan úgy adja meg a sablon URI-JÁT, mint bármely külső központi telepítésnél. A külső sablon lehet egy GitHub-adattár vagy egy külső Storage-fiók.

1. Nyissa meg a Cloud Shell promptot.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell megnyitása":::

1. A sablon üzembe helyezéséhez használja a következő parancsokat:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Helyi sablon üzembe helyezéséhez először fel kell töltenie a sablont a Cloud Shell-munkamenethez kapcsolódó Storage-fiókba.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Válassza ki a Cloud Shell tárfiókját.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Storage-fiók kiválasztása":::

1. Válassza a **fájlmegosztás** lehetőséget.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Fájlmegosztás kiválasztása":::

1. Válassza ki Cloud Shell alapértelmezett fájlmegosztást. A fájlmegosztás nevének formátuma a (z `cs-<user>-<domain>-com-<uniqueGuid>` ).

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Alapértelmezett fájlmegosztás":::

1. Vegyen fel egy új könyvtárat a sablonok tárolására. Válassza ki a könyvtárat.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Könyvtár hozzáadása":::

1. Válassza a **Feltöltés** lehetőséget.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Sablon feltöltése":::

1. Keresse meg és töltse fel a sablont.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Sablon kiválasztása":::

1. Nyissa meg a Cloud Shell promptot.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell megnyitása":::

1. Navigáljon a **clouddrive** könyvtárhoz. Navigáljon a sablonok tárolásához hozzáadott címtárhoz.

1. A sablon üzembe helyezéséhez használja a következő parancsokat:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>További lépések

- További információ az üzembe helyezési parancsokról: [erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI](deploy-cli.md) -vel, valamint [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShellokkal](deploy-powershell.md).
- A sablon telepítésének megkezdése előtt tekintse meg a következő témakört: [ARM-sablon üzembe helyezése](template-deploy-what-if.md)
