---
title: 'Rövid útmutató: Traffic Manager létrehozása Azure Resource Manager sablon használatával (ARM-sablon)'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Traffic Manager-profilt Azure Resource Manager sablon (ARM-sablon) használatával.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: ec569781a6318062810358c2c5e17ba71efc4f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675999"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Rövid útmutató: Traffic Manager profil létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy Traffic Manager-profil létrehozásához külső végpontokkal a teljesítmény-útválasztási módszer használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint) közül származik.

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

A sablonban egyetlen Azure-erőforrás van definiálva:

* [**Microsoft. Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Az Azure Traffic Manager-hoz kapcsolódó további sablonokért tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg megjelenik a-konzolon megjelenő kérdés.

1. A PowerShell-szkript másolásához válassza a **Másolás** az előző kódrészletből lehetőséget.

1. Kattintson a jobb gombbal a rendszerhéj-konzol ablaktáblára, majd válassza a **Beillesztés** lehetőséget.

1. Adja meg az értékeket.

    A sablon üzembe helyezése két külső végponttal rendelkező profilt hoz létre. A **Endpoint1** `www.microsoft.com` az **észak-európai** hellyel rendelkező cél végpontot használja. A **Endpoint2** `docs.microsoft.com` az **USA déli középső** régiójában található célállomás végpontját használja.

    Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel.

    > [!NOTE]
    > a **uniqueDNSname** globálisan egyedi névnek kell lennie ahhoz, hogy a sablon sikeresen üzembe helyezhető. Ha a telepítés sikertelen, kezdje az 1. lépéssel.

    A sablon üzembe helyezése néhány percet vesz igénybe. Ha elkészült, a kimenet a következőhöz hasonló:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager-sablon PowerShell üzembe helyezési kimenete":::

A Azure PowerShell a sablon üzembe helyezésére szolgál. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Határozza meg a Traffic Manager profil DNS-nevét a [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)használatával.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Másolja a **RelativeDnsName** értéket. A Traffic Manager-profil DNS-neve: `<relativednsname>.trafficmanager.net` .

1. Egy helyi PowerShellben futtassa a következő parancsot a **{relativeDNSname}** változónak a paranccsal való lecserélésével `<relativednsname>.trafficmanager.net` .

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    Egy NameHost kell beszereznie, `www.microsoft.com` vagy `docs.microsoft.com` attól függően, hogy melyik régió közelebb van az Ön számára.

1. Annak ellenőrzéséhez, hogy fel tudja-e oldani a másik végpontot, tiltsa le az utolsó lépésben kapott cél végpontját. Cserélje le a **{végpontneve}** parancsot a **endpoint1** vagy a **endpoint2** értékre a vagy a cél letiltásához `www.microsoft.com` `docs.microsoft.com` .

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Futtassa a parancsot a 2. lépésből a helyi PowerShellben. Ezúttal a másik végpont NameHost kell beszereznie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Traffic Manager profilra, törölje az erőforráscsoportot. Ezzel eltávolítja a Traffic Manager profilt és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt.

Ha többet szeretne megtudni az útválasztási forgalomról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
