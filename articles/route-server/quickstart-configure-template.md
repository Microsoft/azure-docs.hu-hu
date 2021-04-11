---
title: 'Gyors útmutató: Azure Route-kiszolgáló létrehozása Azure Resource Manager sablon használatával (ARM-sablon)'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Route Servert Azure Resource Manager sablon (ARM-sablon) használatával.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 6f56b9fb1f6a1f5a1fe0811617fb20412c52fd72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106452201"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Rövid útmutató: Azure Route Server létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy Azure Route-kiszolgáló új vagy meglévő virtuális hálózatra való üzembe helyezéséhez.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-route-server) közül származik.

Ebben a rövid útmutatóban egy Azure Route-kiszolgálót helyez üzembe egy új vagy egy meglévő virtuális hálózatban. A rendszer létrehoz egy nevű dedikált alhálózatot `RouteServerSubnet` az útválasztási kiszolgáló üzemeltetéséhez. Az útvonal-kiszolgáló is a társ ASN-vel és a társ-IP-címmel lesz konfigurálva a BGP-társak létrehozásához.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Több Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/virtualNetworks/Subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (két alhálózat, egy névvel ellátott `routeserversubnet` )
* [**Microsoft. Network/virtualHubs**](/azure.templates/microsoft.network/virtualhubs) (útvonal-kiszolgáló telepítése)
* [**Microsoft. Network/virtualHubs/ipConfigurations**](/azure.templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft. Network/virtualHubs/bgpConnections**](/azure.templates/microsoft.network/virtualhubs/bgpConnections) (társ ASN és társ IP-konfiguráció)


A ExpressRoute kapcsolatos további sablonokért tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Route Server Resource Manager-sablon PowerShell üzembe helyezési kimenete.":::

A Azure PowerShell a sablon üzembe helyezésére szolgál. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve a projekt neve **RG** hozzáfűzéssel.

1. Az erőforráscsoport csak a virtuális hálózatot tartalmazza:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Útválasztó kiszolgáló telepítési erőforráscsoport virtuális hálózattal.":::

1. Nyissa meg a következőt: https://aka.ms/routeserver.

1. Válassza ki a **routeserver** nevű útvonal-kiszolgálót annak ellenőrzéséhez, hogy a központi telepítés sikeres volt-e.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Az útválasztási kiszolgáló áttekintő oldalának képernyőképe.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az útválasztási kiszolgálóval létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az útvonal-kiszolgálót és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* Útvonal-kiszolgáló
* Virtual Network
* Alhálózat

Az Azure Route Server létrehozása után folytassa a további tudnivalókat arról, hogy az Azure Route Server hogyan kommunikál a ExpressRoute és a VPN-átjárókkal: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute és Azure VPN-támogatás](expressroute-vpn-support.md)
