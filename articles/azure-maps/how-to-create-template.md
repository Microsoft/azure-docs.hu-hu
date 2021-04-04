---
title: Azure Maps fiók létrehozása ARM-sablonnal | Microsoft Azure térképek
description: Megtudhatja, hogyan hozhat létre Azure Maps fiókot egy Azure Resource Manager-(ARM-) sablon használatával.
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92525118"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Azure Maps fiók létrehozása ARM-sablonnal

Azure Maps-fiókját egy Azure Resource Manager-(ARM-) sablon használatával is létrehozhatja. A fiók létrehozása után az API-kat a webhelyén vagy a mobil alkalmazásban is megvalósíthatja.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez:

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-maps-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

A Azure Maps fiók erőforrása a következő sablonban van definiálva:

* [**Microsoft. Maps/fiókok**](/azure/templates/microsoft.maps/accounts): hozzon létre egy Azure Maps fiókot.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Maps fiókot.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    ![ARM-sablon üzembe helyezése portál](./media/how-to-create-template/create-account-using-template-portal.png)

    Ha meg van adva, a Azure Maps-fiók létrehozásához használja az alapértelmezett értéket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra.
    * **Hely**: válasszon ki egy helyet. Például: **USA 2. nyugati** régiója.
    * **Fiók neve**: adja meg a Azure Maps-fiók nevét, amelynek globálisan egyedinek kell lennie.
    * **Árképzési szintek**: válassza ki a megfelelő árképzési szintet, a sablon alapértelmezett értéke S0.

3. Válassza az **Áttekintés + létrehozás** lehetőséget. 
4. Erősítse meg a beállításokat a felülvizsgálati oldalon, és kattintson a **Létrehozás** gombra. A Azure Maps sikeres üzembe helyezését követően értesítést kap:

    ![ARM-sablon üzembe helyezése – portál értesítése](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

A Azure Portal a sablon üzembe helyezéséhez használható. Használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal használatával megtekintheti Azure Maps-fiókját, és megtekintheti a kulcsait. A fiók kulcsainak listázásához az alábbi Azure CLI-szkriptet is használhatja.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely szintén törli a Azure Maps fiókot. Az erőforráscsoport törlése az Azure CLI használatával:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Maps és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- Azure Maps [bemutató alkalmazás](quick-demo-map-app.md) létrehozása
- További információ az [ARM-sablonokról](../azure-resource-manager/templates/overview.md)