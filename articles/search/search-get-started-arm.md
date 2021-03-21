---
title: A szolgáltatás üzembe helyezése Azure Resource Manager sablon használatával
titleSuffix: Azure Cognitive Search
description: Az Azure Resource Manager-sablon használatával gyorsan üzembe helyezhet egy Azure Cognitive Search Service-példányt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 11/17/2020
ms.openlocfilehash: 88a081f1ebbda52653302384c06403e223128c60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668384"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Gyors útmutató: Cognitive Search üzembe helyezése ARM-sablon használatával

Ez a cikk végigvezeti egy Azure Resource Manager-sablon (ARM-sablon) használatának folyamatán, amely egy Azure Cognitive Search-erőforrás üzembe helyezését mutatja be a Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-search-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

A sablonban definiált Azure-erőforrás:

- [Microsoft. Search/searchServices](/azure/templates/Microsoft.Search/searchServices): Azure Cognitive Search szolgáltatás létrehozása

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Azure Cognitive Search-erőforrást.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

A portál megjelenít egy űrlapot, amely lehetővé teszi a paraméterek értékének egyszerű megadását. Bizonyos paraméterek előre ki vannak töltve a sablon alapértelmezett értékeivel. Meg kell adnia az előfizetését, az erőforráscsoportot, a helyet és a szolgáltatás nevét. Ha Cognitive Servicest szeretne használni egy AI- [dúsítási](cognitive-search-concept-intro.md) folyamatban, például a bináris képfájlok elemzéséhez a szöveghez, válassza ki a Cognitive Search és a Cognitive Services egyaránt tartalmazó helyet. Mindkét szolgáltatásnak ugyanabban a régióban kell lennie az AI-bővítési munkaterhelések esetében. Az űrlap befejezése után el kell fogadnia a feltételeket és kikötéseket, majd a telepítés befejezéséhez a vásárlás gombot kell választania.

> [!div class="mx-imgBorder"]
> ![A sablon Azure Portal megjelenítése](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az üzembe helyezés befejezésekor elérheti az új erőforráscsoportot és az új keresési szolgáltatást a portálon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutatóban más Cognitive Search rövid útmutatók és oktatóanyagok is felépülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, érdemes lehet ezt az erőforrást helyben hagyni. Ha már nincs rá szükség, törölheti az erőforráscsoportot, amely törli a Cognitive Search szolgáltatást és a kapcsolódó erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Cognitive Search szolgáltatást egy ARM-sablonnal, és ellenőrizte az üzemelő példányt. Ha többet szeretne megtudni a Cognitive Search és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- Olvassa el az [Azure Cognitive Search áttekintése című témakört](search-what-is-azure-search.md).
- [Hozzon létre egy indexet](search-get-started-portal.md) a keresési szolgáltatáshoz.
- [Bemutató alkalmazás létrehozása](search-create-app-portal.md) a portál varázsló használatával.
- [Hozzon létre egy készségkészlet](cognitive-search-quickstart-blob.md) az adatok adatainak kinyeréséhez.
