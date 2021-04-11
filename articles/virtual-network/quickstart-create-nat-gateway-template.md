---
title: NAT-átjáró létrehozása – Resource Manager-sablon
titleSuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót a Azure Resource Manager sablon (ARM-sablon) használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: d1d4cf5a5e616db38885077e70add817f26d125a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060686"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Rövid útmutató: NAT Gateway-ARM-sablon létrehozása

Ismerkedjen meg Virtual Network NAT-val egy Azure Resource Manager sablon (ARM-sablon) használatával. Ez a sablon egy virtuális hálózatot, egy NAT-átjáró erőforrást és egy Ubuntu virtuális gépet helyez üzembe. Az Ubuntu virtuális gép a NAT-átjáró erőforrásához társított alhálózatra van telepítve.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm) közül származik.

Ez a sablon a következő létrehozására van konfigurálva:

* Virtuális hálózat
* NAT-átjáró-erőforrás
* Ubuntu virtuális gép

Az Ubuntu virtuális gép a NAT-átjáró erőforrásához társított alhálózatra van telepítve.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

A sablonban kilenc Azure-erőforrás van definiálva:

* **[Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)**: létrehoz egy hálózati biztonsági csoportot.
* **[Microsoft. Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: biztonsági szabály létrehozása.
* **[Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses)**: létrehoz egy nyilvános IP-címet.
* **[Microsoft. Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)**: létrehoz egy nyilvános IP-előtagot.
* **[Microsoft. számítás/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)**: létrehoz egy virtuális gépet.
* **[Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)**: létrehoz egy virtuális hálózatot.
* **[Microsoft. Network/natGateways](/azure/templates/microsoft.network/natgateways)**: létrehoz egy NAT Gateway-erőforrást.
* **[Microsoft. Network/virtualNetworks/Subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)**: létrehoz egy virtuális hálózati alhálózatot.
* **[Microsoft. Network/networkinterfaces](/azure/templates/microsoft.network/networkinterfaces)**: létrehoz egy hálózati adaptert.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve **myResourceGroupNAT**

1. Ellenőrizze, hogy az erőforráscsoport az alábbi erőforrásokat hozta-e létre:

    ![NAT-erőforráscsoport Virtual Network](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

**Azure CLI**

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Válassza ki a NAT-átjárót tartalmazó erőforráscsoport- **myResourceGroupNAT** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* NAT-átjáró-erőforrás
* Virtuális hálózat
* Ubuntu virtuális gép

A virtuális gép üzembe helyezése a NAT-átjáróhoz társított virtuális hálózati alhálózaton történik.

Ha többet szeretne megtudni a Virtual Network NAT-ról és Azure Resource Managerról, folytassa az alábbi cikkekkel.

* [Virtual Network NAT áttekintése](nat-overview.md)
* További információ a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
* További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
