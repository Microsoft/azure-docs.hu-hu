---
title: Azure igazolási tanúsítvány létrehozása Azure Resource Manager sablon használatával
description: Ismerje meg, hogyan hozhat létre Azure igazolási tanúsítványt Azure Resource Manager sablon használatával.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144968"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Rövid útmutató: Azure igazolási szolgáltató létrehozása ARM-sablonnal

[Microsoft Azure igazolás](overview.md) a megbízható végrehajtási környezetek (pólók) igazolására szolgáló megoldás. Ez a rövid útmutató egy Azure Resource Manager sablon (ARM-sablon) üzembe helyezésének folyamatát mutatja be Microsoft Azure igazolási szabályzat létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-attestation-provider-create) közül származik.

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

A sablonban definiált Azure-erőforrások:

- Microsoft. igazolás/attestationProviders

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez és a sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket.

    Ha meg van adva, az alapértelmezett érték használatával hozza létre az igazolási szolgáltatót.

    - **Igazolási szolgáltató neve**: válassza ki az Azure igazolási szolgáltató nevét.
    - **Hely**: válasszon ki egy helyet. Például: **USA középső régiója**.
    - **Címkék**: válasszon helyet. Például: **USA középső régiója**.

1. Válassza a **Beszerzés** lehetőséget. Miután az igazolási erőforrás üzembe helyezése sikeresen megtörtént, értesítést kap.

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az igazolási erőforrást az Azure Portal használatával tekintheti meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutatón kívül más Azure-igazolás is erre támaszkodik. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az igazolási erőforrást. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával:

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

Ebben a rövid útmutatóban létrehozott egy igazolási erőforrást egy ARM-sablonnal, és ellenőrizte az üzembe helyezést. Az Azure igazolásával kapcsolatos további információkért lásd: [Az Azure-igazolás áttekintése](overview.md).
