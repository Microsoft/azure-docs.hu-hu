---
title: 'Rövid útmutató: Azure Front Door Service létrehozása Azure Resource Manager sablon (ARM-sablon) használatával'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Front Door Service sablon (ARM Azure Resource Manager használatával.
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: de8a592f6eecbb43b58a044096e8ba2e0f9b5973
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539005"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Rövid útmutató: Front Door létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható Azure Resource Manager-sablon (ARM-sablon) egy Front Door webes végpont magas rendelkezésre állásának beállítására.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Egy webhely vagy webalkalmazás IP-címe vagy teljes tartománya.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-front-door-create-basic) közül származik.

Ebben a rövid útmutatóban egy egyetlen háttér Front Door és egyetlen alapértelmezett elérési úttal egyező alapértelmezett elérési úttal fog létrehozni egy új `/*` konfigurációt.

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

A sablonban egyetlen Azure-erőforrás van definiálva:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza **a Try it** (Próbálja ki) lehetőséget az alábbi kódblokkban a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg meg nem látja a parancssort a konzolon.

1. A  PowerShell-szkript másolásához válassza a Másolás lehetőséget az előző kódblokkból.

1. Kattintson a jobb gombbal a rendszerhéj konzolpanelére, majd válassza a Beillesztés **lehetőséget.**

1. Adja meg az értékeket.

    A sablon üzembe helyezése létrehoz egy Front Door egyetlen háttérvel. Ebben a `microsoft.com` példában a **háttércímet használjuk.**

    Az erőforráscsoport neve a projekt neve, **rg hozzáfűzve.**

    > [!NOTE]
    > Ahhoz, hogy a sablon üzembe helyezése sikeres legyen, a **frontDoorName** névnek globálisan egyedi névnek kell lennie. Ha az üzembe helyezés sikertelen, kezdje elölről az 1. lépéssel.

    A sablon üzembe helyezése néhány percet vesz igénybe. Ha elkészült, a kimenet a következőre hasonlít:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door Resource Manager PowerShell-sablon üzembe helyezési kimenete":::

Azure PowerShell a sablon üzembe helyezéséhez használható. A Azure PowerShell mellett használhatja a Azure Portal, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A **bal oldali panelen** válassza az Erőforráscsoportok lehetőséget.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport-név a projekt neve, az **rg hozzáfűzése** után.

1. Válassza Front Door korábban létrehozott gazdagépet, és kattintson az **Előtere gazdagép hivatkozásra.** A hivatkozás megnyit egy webböngészőt, amely átirányítja a létrehozás során meghatározott háttér-FQDN-hez.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door portál áttekintése":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Front Door szolgáltatásra, törölje az erőforráscsoportot. Ezzel eltávolítja a Front Door és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Front Door.

Ha meg szeretne tudni, hogyan adhat hozzá egyéni tartományt a Front Door, folytassa a Front Door oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Front Door oktatóanyagok](front-door-custom-domain.md)
