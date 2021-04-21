---
title: 'Rövid útmutató: Profil és végpont létrehozása – Resource Manager sablon'
titleSuffix: Azure Content Delivery Network
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Content Delivery Network-profilt és végpontot egy Resource Manager sablonhoz
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: 31f0510ed50c9d48a46524a353d7c872b368f75c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779038"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Rövid útmutató: Azure CDN profil és végpont létrehozása – ARM-sablon

Az Azure Content Delivery Network (CDN) használatának első Azure Resource Manager sablon (ARM-sablon) használatával. A sablon üzembe helyez egy profilt és egy végpontot.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/) közül származik.

Ez a sablon a következő létrehozására van konfigurálva:

* Profil
* Végpont

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

A sablonban egyetlen Azure-erőforrás van definiálva:

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portál

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A **bal oldali panelen** válassza az Erőforráscsoportok lehetőséget.

3. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve **myResourceGroupCDN**

4. Ellenőrizze, hogy a következő erőforrások létrejöttek-e az erőforráscsoportban:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN erőforráscsoport" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="azure-cli"></a>Azure CLI

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portál

Ha már nincs rá szükség, törölje az erőforráscsoportot, a CDN-profilt és az összes kapcsolódó erőforrást. Válassza ki a CDN-profilt és -végpontot tartalmazó **myResourceGroupCDN** erőforráscsoportot, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:

* CDN-profil
* Végpont

Ha többet szeretne megtudni a Azure CDN és Azure Resource Manager, folytassa az alábbi cikkekkel.

> [!div class="nextstepaction"]
> [Oktatóanyag: Statikus tartalom szolgáltatása webalkalmazásból a CDN használatával](cdn-add-to-web-app.md)
