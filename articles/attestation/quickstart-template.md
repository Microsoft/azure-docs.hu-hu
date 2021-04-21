---
title: Tanúsítvány Azure Attestation sablon használatával Azure Resource Manager létrehozása
description: Megtudhatja, hogyan hozhat létre Azure Attestation tanúsítványt egy Azure Resource Manager használatával.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 7d70f9ebd071d6699412f56e9dca1abcebb38105
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834245"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Rövid útmutató: Azure Attestation létrehozása ARM-sablonnal

[Microsoft Azure igazolás](overview.md) a megbízható végrehajtási környezetek (TEE-k) igazolásának egyik megoldása. Ez a rövid útmutató egy Azure Resource Manager (ARM-sablon) üzembe helyezésének folyamatára összpontosít egy Microsoft Azure igazolási szabályzat létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-attestation-provider-create) közül származik.

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

A sablonban definiált Azure-erőforrások:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az alábbi kép kiválasztásával jelentkezzen be az Azure-ba, és nyissa meg a sablont.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket.

    Ha nincs megadva, használja az alapértelmezett értéket az igazolási szolgáltató létrehozásához.

    - **Igazolásszolgáltató neve:** Válassza ki a Azure Attestation nevét.
    - **Hely:** Válasszon ki egy helyet. Például: **USA középső régiója**.
    - **Címkék:** Válasszon ki egy helyet. Például: **USA középső régiója**.

1. Válassza a **Beszerzés** lehetőséget. Az igazolási erőforrás sikeres üzembe helyezése után értesítést kap.

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portal mellett az Azure PowerShell, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az igazolási erőforrás Azure Portal a következővel ellenőrizheti: .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Más Azure Attestation erre a rövid útmutatóra épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az igazolási erőforrást. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy igazolási erőforrást egy ARM-sablonnal, és érvényesítette az üzembe helyezést. További információ a [Azure Attestation: Overview of Azure Attestation](overview.md).
