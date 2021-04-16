---
title: 'Rövid útmutató: Webes forgalom közvetlen használata Resource Manager sablonnal'
titleSuffix: Azure Application Gateway
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Resource Manager-sablont olyan Azure Application Gateway, amely a webes forgalmat egy háttérkészletben lévő virtuális gépekre irányítja.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: dd100361ba5d4ff175e340ced782999e52c720c4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538433"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Rövid útmutató: Webes forgalom Azure Application Gateway – ARM-sablon

Ebben a rövid útmutatóban egy Azure Resource Manager (ARM-sablon) használatával fog létrehozni egy Azure Application Gateway. Ezután tesztelje az Alkalmazásátjárót, hogy ellenőrizze, megfelelően működik-e.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a következő használatával [](quick-create-powershell.md)is Azure Portal: , [Azure PowerShell](quick-create-portal.md)vagy [Azure CLI](quick-create-cli.md)használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>A sablon áttekintése

Az egyszerűség kedvéért ez a sablon egy egyszerű beállítást hoz létre egy nyilvános előtere IP-címmel, egy alapszintű listenerrel, amely egyetlen helyet kezel az alkalmazásátjárón, egy egyszerű kérés-útválasztási szabályt és két virtuális gépet a háttérkészletben.

Az ebben a rövid útmutatóban használt sablon az [Azure gyorsindítási sablonokból származó](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

A sablonban több Azure-erőforrás is definiálva van:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses: egy**](/azure/templates/microsoft.network/publicipaddresses) az alkalmazásátjáróhoz és kettő a virtuális gépekhez.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/microsoft.compute/virtualmachines) két virtuális gép
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/microsoft.network/networkinterfaces) kettő a virtuális gépekhez
- [**Microsoft.Compute/virtualMachine/extensions:**](/azure/templates/microsoft.compute/virtualmachines/extensions) az IIS és a weblapok konfigurálása

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Az **Azure-ba való bejelentkezéshez** és a sablon megnyitásához válassza az Üzembe helyezés az Azure-ban lehetőséget. A sablon létrehoz egy Alkalmazásátjárót, a hálózati infrastruktúrát és két virtuális gépet az IIS-t futtató háttérkészletben.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot, majd írja be a virtuális gép rendszergazdai felhasználónevét és jelszavát.
3. Válassza **az Áttekintés + létrehozás,** majd a Létrehozás **lehetőséget.**

   Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Bár az IIS nem szükséges az Alkalmazásátjáró létrehozásához, telepítve van annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. Használja az IIS-t az Application Gateway tesztelésére:

1. Keresse meg az Alkalmazásátjáró nyilvános IP-címét az **Áttekintés lapon.** ![ Rögzítse az Application Gateway nyilvános IP-címét Vagy válassza a Minden erőforrás lehetőséget, írja be a ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) *myAGPublicIPAddress* címet a keresőmezőbe, majd válassza ki a keresési eredmények között.  Az Azure az Áttekintés oldalon jeleníti meg a nyilvános **IP-címet.**
2. Másolja ki a nyilvános IP-címet, majd illessze be a böngésző címsorába az IP-cím tallózáshoz.
3. Ellenőrizze a választ. Egy érvényes válasz ellenőrzi, hogy az Application Gateway sikeresen létrejött-e, és sikeresen tud-e csatlakozni a háttérkiszolgálóhoz.

   ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Frissítse többször a böngészőt, és látnia kell a myVM1 és a myVM2 kapcsolatát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az Application Gatewayt és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)
