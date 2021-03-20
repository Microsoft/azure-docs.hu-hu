---
title: Rövid útmutató – Azure API Management-példány létrehozása ARM-sablon használatával
description: Megtudhatja, hogyan hozhat létre Azure API Management-példányt a fejlesztői szinten Azure Resource Manager sablon (ARM-sablon) használatával.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792242"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Rövid útmutató: új Azure API Management Service-példány létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy Azure API Management-(APIM-) szolgáltatási példány létrehozásához. A APIM segítségével a szervezetek külső, partneri és belső fejlesztők számára is közzétehetik az API-kat, hogy feloldják az adataikat és szolgáltatásaikat. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. A APIM lehetővé teszi modern API Gateway-átjárók létrehozását és felügyeletét a bárhol üzemeltetett meglévő háttér-szolgáltatásokhoz. További információ: [Áttekintés](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

A következő erőforrás van definiálva a sablonban:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

További Azure API Management-sablonok az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular)találhatók.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy API Management Service-példányt egy automatikusan generált névvel.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    Ebben a példában a példány a fejlesztői szinten van konfigurálva, amely az Azure-API Management kiértékelésére szolgáló gazdaságos megoldás. Ez a rétegek nem használhatók éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál.

1. Válassza ki vagy adja meg a következő értékeket.
    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK gombra**.
    - **Régió**: válassza ki az erőforráscsoport helyét. Példa: **USA középső** régiója.
    - **Közzétevői e-mail**: adjon meg egy e-mail-címet az értesítések fogadásához.
    - **Közzétevő neve**: adjon meg egy nevet, amelyet az API-közzétevőhöz választ.
    - **SKU**: fogadja el a **fejlesztő** alapértelmezett értékét.
    - **SKU-darabszám**: fogadja el az alapértelmezett értéket.
    - **Hely**: fogadja el a API Management szolgáltatás létrehozott helyét.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="API Management sablon tulajdonságai":::

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd tekintse át a használati feltételeket. Ha elfogadja, válassza a **Létrehozás** lehetőséget.

    > [!TIP]
    >  A API Management szolgáltatás létrehozása és aktiválása a fejlesztői szinten 30 és 40 percet is igénybe vehet.

1. A példány sikeres létrehozása után értesítést kap:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Üzembe helyezési értesítés":::

 Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal használatával tekintheti meg az üzembe helyezett erőforrásokat, vagy használhat olyan eszközöket, mint például az Azure CLI vagy a Azure PowerShell az üzembe helyezett erőforrások listázásához.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **API Management szolgáltatások** elemet, majd válassza ki a létrehozott szolgáltatási példányt.
1. Tekintse át a szolgáltatás tulajdonságait az **Áttekintés** oldalon.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Szolgáltatás – Áttekintés lap":::

Ha a API Management Service-példány online állapotban van, készen áll a használatára. Kezdje az Oktatóanyaggal az első API [importálásához és közzétételéhez](import-and-publish.md) .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő oktatóanyagokkal folytatja a munkát, érdemes lehet a API Management példányt a helyén hagyni. Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet. A **kezdőlapon** kiválaszthatja az **erőforráscsoportok** lehetőséget is.
1. Az **erőforráscsoportok** lapon válassza ki az erőforráscsoportot.
1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése** elemet.

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="Erőforráscsoport törlése":::
1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: az első API importálása és közzététele](import-and-publish.md)
