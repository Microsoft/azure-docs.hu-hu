---
title: 'Rövid útmutató: Azure Route Server létrehozása Azure Resource Manager sablon (ARM-sablon) használatával'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Route Servert egy Azure Resource Manager (ARM-sablon) használatával.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388040"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Rövid útmutató: Azure Route Server létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy Azure Route Server új vagy meglévő virtuális hálózaton való üzembe helyezésére.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-route-server) közül származik.

Ebben a rövid útmutatóban egy Azure Route Servert fog üzembe helyezni egy új vagy meglévő virtuális hálózaton. A rendszer létrehoz egy nevű dedikált `RouteServerSubnet` alhálózatot az útvonalkiszolgáló gazdagépeként. Az útvonalkiszolgáló a társ ASN-ével és a társ IP-címmel is konfigurálva lesz egy BGP-társviszony létesítéséhez.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

A sablonban több Azure-erőforrás is definiálva van:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/alhálózatok**](/azure/templates/microsoft.network/virtualNetworks/subnets) (két alhálózat, egy `routeserversubnet` nevű)
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (Útvonalkiszolgáló üzembe helyezése)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (társ ASN és társ IP-konfiguráció)


Az ExpressRoute-hoz kapcsolódó további sablonokért lásd: [Azure gyorsindítási sablonok.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza **a Próbálja ki az** alábbi kódblokkban lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg meg nem látja a parancssort a konzolon.

1. A  PowerShell-szkript másolásához válassza a Másolás lehetőséget az előző kódblokkból.

1. Kattintson a jobb gombbal a rendszerhéj konzolpanelére, majd válassza a Beillesztés **lehetőséget.**

1. Adja meg az értékeket.

    Az erőforráscsoport neve a projekt neve, **rg hozzáfűzve.**

    A sablon üzembe helyezése körülbelül 20 percet vesz igénybe. Ha elkészült, a kimenet a következő hasonló lesz:

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Útvonalkiszolgáló Resource Manager PowerShell-sablon üzembe helyezési kimenete.":::

Azure PowerShell a sablon üzembe helyezéséhez. A Azure PowerShell mellett használhatja a Azure Portal, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A **bal oldali panelen** válassza az Erőforráscsoportok lehetőséget.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve a projekt neve, az **rg hozzáfűzése** után.

1. Az erőforráscsoportnak csak a virtuális hálózatot kell tartalmaznia:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Útvonalkiszolgáló üzembe helyezési erőforráscsoportja virtuális hálózattal.":::

1. Nyissa meg a következőt: https://aka.ms/routeserver.

1. Válassza ki a routeserver nevű **útválasztási** kiszolgálót az üzembe helyezés sikeres állapotának ellenőrzéséhez.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Az Útvonalkiszolgáló áttekintő oldalának képernyőképe.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az útvonalkiszolgálóval létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az útvonalkiszolgálót és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:

* Útvonalkiszolgáló
* Virtual Network
* Alhálózat

Az Azure Route Server létrehozása után további információkkal szolgál arról, hogyan kommunikál az Azure Route Server az ExpressRoute-ral és a VPN-átjáróval: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute és Az Azure VPN támogatása](expressroute-vpn-support.md)
