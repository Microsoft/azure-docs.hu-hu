---
title: 'Gyors útmutató: profil és végpont – Resource Manager-sablon létrehozása'
titleSuffix: Azure Content Delivery Network
description: Ismerje meg, hogyan hozhat létre Azure Content Delivery Network-profilt és egy Resource Manager-sablont a végponton
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
ms.openlocfilehash: ca74c5f54cfb513f4611fafc40d34378707ff949
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746488"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Gyors útmutató: Azure CDN profil és végpont-ARM sablon létrehozása

Az Azure Content Delivery Network (CDN) használatának első lépései Azure Resource Manager sablon (ARM-sablon) használatával. A sablon egy profilt és egy végpontot telepít.

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

* **[Microsoft. CDN/profilok](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
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

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

3. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve **myResourceGroupCDN**

4. Ellenőrizze, hogy az erőforráscsoport az alábbi erőforrásokat hozta-e létre:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN erőforráscsoport" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

### <a name="azure-cli"></a>Azure CLI

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Ha már nincs rá szükség, a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portál

Ha már nincs rá szükség, törölje az erőforráscsoportot, a CDN-profilt és az összes kapcsolódó erőforrást. Válassza ki a CDN-profilt és-végpontot tartalmazó erőforráscsoport **myResourceGroupCDN** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* CDN-profil
* Végpont

Ha többet szeretne megtudni a Azure CDN és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

* [A Azure CDN áttekintése](cdn-overview.md)
* További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
