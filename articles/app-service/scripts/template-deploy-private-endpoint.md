---
title: Privát Azure Resource Manager üzembe helyezése webalkalmazáshoz sablon használatával
description: Ismerje meg, hogyan helyezhet üzembe egy privát végpontot a webalkalmazáshoz ARM-sablonnal.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df71c1a92840ae0e7fa263e2ababcf5b3e059789
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832535"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Hozzon létre App Service alkalmazást, és telepítsen egy privát végpontot egy Azure Resource Manager használatával

Ebben a rövid útmutatóban egy Azure Resource Manager (ARM) sablonnal hoz létre egy webalkalmazást, és elérhetővé teszi azt egy privát végponttal.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Előfeltétel

Szüksége van egy aktív előfizetéssel rendelkezik Azure-fiókra. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ez a sablon létrehoz egy privát végpontot egy Azure-webalkalmazáshoz.

### <a name="review-the-template"></a>A sablon áttekintése

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az azure-beli Azure Resource Manager üzembe helyezése a következő:

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza a következő hivatkozást: [Üzembe helyezés az Azure-ban.](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json) A sablon létrehozza a virtuális hálózatot, a webalkalmazást, a privát végpontot és a privát DNS-zónát.
2. Válassza ki vagy hozza létre az erőforráscsoportot.
3. Adja meg a webalkalmazás nevét, Azure App Service csomagját és privát végpontját.
5. Olvassa el a használati feltételekre vonatkozó utasítást. Ha elfogadja, válassza az **Elfogadom a** vásárlást fentebb említett  >  **feltételeket.** Az üzembe helyezés több percig is eltarthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a privát végponttal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a privát végpontot és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

- Az ARM-sablonmintákban Azure Resource Manager webalkalmazások Azure App Service további [sablonokat találhat.](../samples-resource-manager-templates.md)
