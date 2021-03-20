---
title: Privát végpont üzembe helyezése egy webalkalmazáshoz Azure Resource Manager sablon használatával
description: Megtudhatja, hogyan helyezhet üzembe egy privát végpontot a webalkalmazáshoz az ARM-sablon használatával.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652018"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>App Service-alkalmazás létrehozása és privát végpont üzembe helyezése Azure Resource Manager-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager (ARM) sablonnal hozhat létre webalkalmazást, és közzéteheti azt egy privát végponttal.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Előfeltétel

Aktív előfizetéssel rendelkező Azure-fiókra van szüksége. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ez a sablon létrehoz egy privát végpontot egy Azure-webalkalmazáshoz.

### <a name="review-the-template"></a>A sablon áttekintése

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Azure Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza a következő hivatkozást:  [üzembe helyezés az Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)-ban. A sablon létrehozza a virtuális hálózatot, a webalkalmazást, a magánhálózati végpontot és a magánhálózati DNS-zónát.
2. Válassza ki vagy hozza létre az erőforráscsoportot.
3. Adja meg a webalkalmazás nevét, a Azure App Service-csomagot és a magánhálózati végpontot.
5. Olvassa el a feltételekről és kikötésekről szóló nyilatkozatot. Ha elfogadja, jelölje be az Elfogadom a vásárláskor **megjelenő feltételeket és kikötéseket**  >  . Az üzembe helyezés több percet is igénybe vehet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a privát végponttal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a magánhálózati végpontot és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

- Az [ARM-sablonokban](../samples-resource-manager-templates.md)további Azure Resource Manager sablonokat találhat Azure app Service Web Apps számára.
