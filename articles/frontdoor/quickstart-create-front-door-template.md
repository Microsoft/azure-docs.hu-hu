---
title: 'Rövid útmutató: Azure-beli bejárati szolgáltatás létrehozása Azure Resource Manager sablon használatával (ARM-sablon)'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure-beli bejárati szolgáltatást Azure Resource Manager sablon (ARM-sablon) használatával.
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 223006193219afe4179f3161d5e60e6439207b22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896055"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Rövid útmutató: első ajtó létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy bejárati ajtó létrehozásához egy webes végpont magas rendelkezésre állásának beállításához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Webhely vagy webalkalmazás IP-címe vagy teljes tartományneve.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-front-door-create-basic) közül származik.

Ebben a rövid útmutatóban egy előtérben lévő konfigurációt hoz létre egyetlen háttérrel és egyetlen alapértelmezett elérési úttal `/*` .

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

A sablonban egyetlen Azure-erőforrás van definiálva:

* [**Microsoft. Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg megjelenik a-konzolon megjelenő kérdés.

1. A PowerShell-szkript másolásához válassza a **Másolás** az előző kódrészletből lehetőséget.

1. Kattintson a jobb gombbal a rendszerhéj-konzol ablaktáblára, majd válassza a **Beillesztés** lehetőséget.

1. Adja meg az értékeket.

    A sablon üzembe helyezése egy bejárati ajtót hoz létre egyetlen háttérrel. Ebben a példában `microsoft.com` a **backendAddress** használják.

    Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel.

    > [!NOTE]
    > a **frontDoorName** globálisan egyedi névnek kell lennie ahhoz, hogy a sablon sikeresen üzembe helyezhető. Ha a telepítés sikertelen, kezdje az 1. lépéssel.

    A sablon üzembe helyezése néhány percet vesz igénybe. Ha elkészült, a kimenet a következőhöz hasonló:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Az előtérben lévő Resource Manager-sablon PowerShell-telepítésének kimenete":::

A Azure PowerShell a sablon üzembe helyezésére szolgál. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve a projekt neve **RG** hozzáfűzéssel.

1. Válassza ki a korábban létrehozott bejárati ajtót, és kattintson a **frontend Host** hivatkozásra. A hivatkozás megnyit egy webböngészőt, amely átirányítja a háttérbeli FQDN-re, amelyet a létrehozás során megadott.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Az első ajtós portál áttekintése":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a bejárati ajtó szolgáltatásra, törölje az erőforráscsoportot. Ezzel eltávolítja a bejárati ajtót és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy bejárati ajtót.

Ha szeretné megtudni, hogyan adhat hozzá egyéni tartományt a bejárati ajtóhoz, folytassa az előtérben lévő oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Útmutató a bejárati ajtókhoz](front-door-custom-domain.md)
