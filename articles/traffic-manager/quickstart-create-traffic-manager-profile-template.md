---
title: 'Rövid útmutató: Traffic Manager létrehozása Azure Resource Manager sablon (ARM-sablon) használatával'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Traffic Manager profilt egy Azure Resource Manager (ARM-sablon) használatával.
services: traffic-manager
author: duongau
ms.author: duau
ms.date: 09/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 03342d33731ed29b60908044cd29d529aaa5677b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531244"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Rövid útmutató: Traffic Manager profil létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható Azure Resource Manager-sablon (ARM-sablon) külső végpontokkal Traffic Manager-profil létrehozására a teljesítmény-útválasztási módszerrel.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint) közül származik.

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

A sablonban egyetlen Azure-erőforrás van definiálva:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

A sablonokkal kapcsolatos további sablonok Azure Traffic Manager [Azure gyorsindítási sablonok.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza **a Try it** (Próbálja ki) lehetőséget az alábbi kódblokkban a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg meg nem látja a parancssort a konzolon.

1. A  PowerShell-szkript másolásához válassza a Másolás lehetőséget az előző kódblokkból.

1. Kattintson a jobb gombbal a rendszerhéj konzolpanelére, majd válassza a Beillesztés **lehetőséget.**

1. Adja meg az értékeket.

    A sablon üzembe helyezése létrehoz egy profilt két külső végponttal. **Az 1.** végpont a célvégpontját `www.microsoft.com` használja észak-európai **helyről.** **A 2.** végpont a célvégpontját `docs.microsoft.com` használja az USA déli középső részén található **helyére.**

    Az erőforráscsoport neve a projekt neve, **rg hozzáfűzve.**

    > [!NOTE]
    > Ahhoz, hogy a sablon sikeresen üzembe helyezhető legyen, a **uniqueDNSname** névnek globálisan egyedi névnek kell lennie. Ha az üzembe helyezés sikertelen, kezdje elölről az 1. lépéssel.

    A sablon üzembe helyezése néhány percet vesz igénybe. Ha elkészült, a kimenet a következőre hasonlít:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager PowerShell-sablon üzembe helyezési kimenete":::

Azure PowerShell a sablon üzembe helyezéséhez használható. A Azure PowerShell mellett használhatja a Azure Portal, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Határozza meg a profil DNS Traffic Manager a [Get-AzTrafficManagerProfile használatával.](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Másolja ki **a RelativeDnsName értéket.** A profil DNS-Traffic Manager a `<relativednsname>.trafficmanager.net` következő: .

1. Egy helyi PowerShellben futtassa a következő parancsot úgy, hogy lecseréli a **{relativeDNSname}** változót a `<relativednsname>.trafficmanager.net` következőre: .

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    A NameHost a vagy a et kell kapnia attól függően, `www.microsoft.com` hogy melyik régió közelebb van az `docs.microsoft.com` Önhez.

1. Annak ellenőrzéshez, hogy tud-e feloldani egy másik végpontot, tiltsa le az előző lépésben kapott cél végpontját. Cserélje le **az {endpointName}** helyére az **endpoint1** vagy **az endpoint2** végpontot, hogy letiltsa a célt a `www.microsoft.com` vagy a `docs.microsoft.com` számára.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Futtassa ismét a parancsot a 2. lépésben egy helyi PowerShellben. Ezúttal a másik végponthoz a másik NameHost nevet kell kapnia.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Traffic Manager profilra, törölje az erőforráscsoportot. Ezzel eltávolítja a Traffic Manager profilt és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt.

Ha többet szeretne megtudni a forgalom útválasztásról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
