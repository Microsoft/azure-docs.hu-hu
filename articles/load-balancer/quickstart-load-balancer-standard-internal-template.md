---
title: 'Rövid útmutató: Belső terheléselosztás létrehozása sablon használatával'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre belső Azure Load Balancert egy Azure Resource Manager sablon (ARM-sablon) használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 00126dde55ffe584be611ddf268bb759e127d7a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788744"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Rövid útmutató: Belső terheléselosztás létrehozása a virtuális gépek ARM-sablonnal való terheléselosztása érdekében

Ez a rövid útmutató azt ismerteti, hogyan használható Azure Resource Manager sablon (ARM-sablon) egy belső Azure-terheléselosztás létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure gyorsindítási sablonokból áll.](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

A sablonban több Azure-erőforrás is definiálva van:

- [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)Virtuálisgép-tárfiókok rendszerindítási diagnosztikához.
- [**Microsoft.Compute/availabilitySets:**](/azure/templates/microsoft.compute/availabilitySets)Rendelkezésre állási készlet virtuális gépekhez.
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/microsoft.network/virtualNetworks)Virtuális hálózat terheléselosztáshoz és virtuális gépekhez.
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/microsoft.network/networkInterfaces)Virtuális gépek hálózati adapterei.
- [**Microsoft.Network/loadBalancers:**](/azure/templates/microsoft.network/loadBalancers)Belső terheléselosztás.
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/microsoft.compute/virtualMachines)Virtuális gépek.

A szolgáltatáshoz kapcsolódó további sablonok Azure Load Balancer [Azure gyorsindítási sablonok.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A **bal oldali panelen** válassza az Erőforráscsoportok lehetőséget.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve **myResourceGroupLB**

1. Ellenőrizze, hogy a következő erőforrások létrejöttek-e az erőforráscsoportban:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="A Azure Portal erőforrások létrehozásának ellenőrzéséhez." border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Következő lépések

A sablonok létrehozásának folyamatát részletesen ismertető oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
