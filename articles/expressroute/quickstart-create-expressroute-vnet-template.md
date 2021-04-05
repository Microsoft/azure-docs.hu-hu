---
title: 'Gyors útmutató: ExpressRoute-kör létrehozása Azure Resource Manager sablon használatával (ARM-sablon)'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy ExpressRoute-áramkört Azure Resource Manager sablon (ARM-sablon) használatával.
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 7521344a2bb6aae67724c8bfbb9131e2ff1e6b94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789726"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Gyors útmutató: ExpressRoute-áramkör létrehozása egy ARM-sablon használatával

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy ExpressRoute-áramkör létrehozásához privát társsal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet) közül származik.

Ebben a rövid útmutatóban egy ExpressRoute-áramkört fog létrehozni a *Equinix* -ben a szolgáltatóként. Az áramkör *prémium SKU*-t, *50 Mbps* sávszélességet és a *Washington DC* egyenrangú helyét fogja használni. A privát társítás a *192.168.10.16/30* és a *192.168.10.20/30* elsődleges és másodlagos alhálózattal lesz engedélyezve. A virtuális hálózatok a *HighPerformance ExpressRoute-átjáróval* együtt is létrejönnek.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft. Network/expressRouteCircuits/társaik**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (az áramkörön a privát társak engedélyezésére használják)
* [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (hálózati biztonsági csoport a virtuális hálózat alhálózatai esetében)
* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicIPAddresses) (a nyilvános IP-címet a ExpressRoute-átjáró használja)
* [**Microsoft. Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (a ExpressRoute-átjáró a VNet kapcsolására használatos az áramkörhöz)

A ExpressRoute kapcsolatos további sablonokért tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Várjon, amíg megjelenik a-konzolon megjelenő kérdés.

1. A PowerShell-szkript másolásához válassza a **Másolás** az előző kódrészletből lehetőséget.

1. Kattintson a jobb gombbal a rendszerhéj-konzol ablaktáblára, majd válassza a **Beillesztés** lehetőséget.

1. Adja meg az értékeket.

    Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel.

    A sablon üzembe helyezése körülbelül 20 percet vesz igénybe. Ha elkészült, a kimenet a következőhöz hasonló:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Resource Manager-sablon PowerShell üzembe helyezési kimenete":::

A Azure PowerShell a sablon üzembe helyezésére szolgál. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve a projekt neve **RG** hozzáfűzéssel.

1. Az erőforráscsoport az alábbi, itt látható erőforrásokat tartalmazza:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute üzembe helyezési erőforráscsoport":::

1. Válassza ki az ExpressRoute áramkör **er-ck01** annak ellenőrzéséhez, hogy az áramkör állapota **engedélyezve** van-e, a szolgáltató állapota nincs **kiépítve** , és a privát társítás állapota **kiépítve**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute üzembe helyezési áramkör":::

> [!NOTE]
> A virtuális hálózat áramkörhöz való összekapcsolásához meg kell hívnia a szolgáltatót a létesítési folyamat befejezéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a ExpressRoute áramkörrel létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a ExpressRoute áramkört és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* ExpressRoute-kapcsolatcsoport
* Virtual Network
* VPN Gateway
* Nyilvános IP-cím
* hálózati biztonsági csoportok

Ha szeretné megtudni, hogyan kapcsolhat egy virtuális hálózatot egy áramkörhöz, folytassa a ExpressRoute oktatóanyagokkal.

> [!div class="nextstepaction"]
> [ExpressRoute oktatóanyagok](expressroute-howto-linkvnet-portal-resource-manager.md)
