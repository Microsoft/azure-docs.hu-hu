---
title: 'Rövid útmutató: ExpressRoute-kapcsolatkör létrehozása egy Azure Resource Manager sablon (ARM-sablon) használatával'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Egy ExpressRoute-kapcsolatkört egy Azure Resource Manager (ARM-sablon) használatával.
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3dc1d5f5ec3dfb004468deb2bec80927c7ec189d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529884"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Rövid útmutató: ExpressRoute-kapcsolathálózat létrehozása privát társviszony-létesítés használatával ARM-sablon használatával

Ez a rövid útmutató azt ismerteti, hogyan használható Azure Resource Manager sablon (ARM-sablon) privát társviszony-létesítésű ExpressRoute-kapcsolathálózat létrehozására.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet) közül származik.

Ebben a rövid útmutatóban egy ExpressRoute-kapcsolatkört fog létrehozni szolgáltatóként *az Equinix* szolgáltatással. A kapcsolati kapcsolat egy *prémium szintű termékváltozatot* *használ, 50 Mbps* sávszélességgel, és *Washington D.C. társviszony-létesítési helyével.* A privát társviszony-létesítés a *192.168.10.16/30* és *a 192.168.10.20/30* elsődleges és másodlagos alhálózaton lesz engedélyezve. Egy virtuális hálózat is létrejön egy *HighPerformance ExpressRoute-átjáróval együtt.*

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

A sablonban több Azure-erőforrás is definiálva van:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (a privát társviszony-létesítés a kapcsolaton való engedélyezése érdekében)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (a hálózati biztonsági csoport a virtuális hálózat alhálózataira vonatkozik)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (az ExpressRoute-átjáró nyilvános IP-címet használ)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (Az ExpressRoute-átjáró a virtuális hálózat és a kapcsolati kapcsolat kapcsolatának összekötését használja)

Az ExpressRoute-hoz kapcsolódó további sablonokért lásd: [Azure gyorsindítási sablonok.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza **a Try it** (Próbálja ki) lehetőséget az alábbi kódblokkban a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

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

    A sablon üzembe helyezése körülbelül 20 percet vesz igénybe. Ha elkészült, a kimenet a következőre hasonlít:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Resource Manager PowerShell-sablon üzembe helyezési kimenete":::

Azure PowerShell a sablon üzembe helyezéséhez használható. A Azure PowerShell mellett használhatja a Azure Portal, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A **bal oldali panelen** válassza az Erőforráscsoportok lehetőséget.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport-név a projekt neve, az **rg hozzáfűzése** után.

1. Az erőforráscsoportnak az alábbi itt látható erőforrásokat kell tartalmaznia:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute üzembe helyezési erőforráscsoport":::

1. Válassza ki az **er-ck01** ExpressRoute-kapcsolatkört annak ellenőrzéséhez, hogy a kapcsolatkör állapota **Engedélyezve,** a szolgáltató állapota **Nincs** kiépítve, a privát társviszony-létesítés állapota Pedig **Kiépítve.**

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute-üzembehelyhelyi kapcsolati kör":::

> [!NOTE]
> A kiépítési folyamat befejezéséhez meg kell hívnia a szolgáltatót, mielőtt a virtuális hálózatot a kapcsolati kapcsolathoz kapcsolhatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ExpressRoute-kapcsolatcsoporttal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az ExpressRoute-kapcsolatkört és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:

* ExpressRoute-kapcsolatcsoport
* Virtual Network
* VPN Gateway
* Nyilvános IP-cím
* hálózati biztonsági csoportok

Ha meg szeretne ismerkedni a virtuális hálózatok kapcsolati kapcsolatokkal való csatolásának mikéntjével, folytassa az ExpressRoute oktatóanyagokkal.

> [!div class="nextstepaction"]
> [ExpressRoute-oktatóanyagok](expressroute-howto-linkvnet-portal-resource-manager.md)
