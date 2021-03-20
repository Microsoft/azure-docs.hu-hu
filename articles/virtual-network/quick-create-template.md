---
title: 'Rövid útmutató: virtuális hálózat létrehozása Resource Manager-sablonnal'
titleSuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre Azure-beli virtuális hálózatot egy Resource Manager-sablon használatával.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217668"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Gyors útmutató: virtuális hálózat létrehozása – Resource Manager-sablon

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre két alhálózattal rendelkező virtuális hálózatot a Azure Resource Manager sablon használatával. A virtuális hálózat az Azure-beli magánhálózat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek számára, hogy biztonságosan kommunikáljanak egymással és az internettel.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a [Azure Portal](quick-create-portal.md), a [Azure PowerShell](quick-create-powershell.md)vagy az [Azure CLI](quick-create-cli.md)használatával is elvégezheti.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

A következő Azure-erőforrások vannak definiálva a sablonban:
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): hozzon létre egy Azure-beli virtuális hálózatot.
-  [**Microsoft. Network/virtualNetworks/Subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets) – alhálózat létrehozása.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon két alhálózattal rendelkező virtuális hálózatot hoz létre.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. A portálon, az **Virtual Network létrehozása két alhálózattal** lapon írja be vagy válassza ki a következő értékeket:
   - **Erőforráscsoport**: válassza az **új létrehozása** elemet, írja be az erőforráscsoport nevét, majd kattintson **az OK gombra**.
   - **Virtual Network neve**: írja be az új virtuális hálózat nevét.
3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Ismerkedjen meg a virtuális hálózattal létrehozott erőforrásokkal.

A sablonban található virtuális hálózatok JSON-szintaxisáról és tulajdonságairól a [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)című témakörben olvashat bővebben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a virtuális hálózattal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a virtuális hálózatot és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban üzembe helyezett egy Azure-beli virtuális hálózatot két alhálózattal. Ha többet szeretne megtudni az Azure Virtual Networks szolgáltatásról, folytassa a virtuális hálózatok oktatóanyagával.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md)