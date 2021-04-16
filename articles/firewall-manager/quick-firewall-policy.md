---
title: 'Rövid útmutató: Új Azure Firewall és tűzfal-szabályzat létrehozása – Resource Manager sablon'
description: Ebben a rövid útmutatóban üzembe helyez egy Azure Firewall és egy tűzfal-szabályzatot.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529866"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Rövid útmutató: Azure Firewall és tűzfal-szabályzat létrehozása – ARM-sablon

Ebben a rövid útmutatóban egy Azure Resource Manager (ARM-sablon) használatával fog létrehozni egy Azure Firewall és egy tűzfal-szabályzatot. A tűzfalszabálynak van egy olyan alkalmazásszabálya, amely engedélyezi a csatlakozást a szolgáltatáshoz, és egy olyan s szabály, amely engedélyezi `www.microsoft.com` a **Windows Update WindowsUpdate** FQDN címkével történő csatlakozást. A hálózati szabályok 13.86.101.172 időpontban engedélyezik az UDP-kapcsolatokat egy időkiszolgálóhoz.

A szabályok ip-csoportokat is használnak a forrás **IP-címek** meghatározásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

További információ a [Azure Firewall Manager: Mi a Azure Firewall Manager?](overview.md).

További információ a [Azure Firewall: Mi a Azure Firewall?](../firewall/overview.md).

További információ az IP-csoportokról: [IP Groups in Azure Firewall](../firewall/ip-groups.md).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon egy központi virtuális hálózatot hoz létre a forgatókönyv támogatásához szükséges erőforrásokkal együtt.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

A sablonban több Azure-erőforrás is definiálva van:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Válassza **az Üzembe helyezés az Azure-ban** lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Azure Firewall, egy virtuális WAN-t és virtuális központot, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. A portálon a Tűzfal és tűzfalszabályság létrehozása szabályokkal és **IP-csoportokkal** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: Válasszon a meglévő előfizetések közül.
   - Erőforráscsoport: Válasszon a meglévő erőforráscsoportok közül, vagy válassza az **Új létrehozása** lehetőséget, majd kattintson az **OK gombra.**
   - Régió: Válasszon ki egy régiót.
   - Tűzfal neve: adja meg a tűzfal nevét.

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget. Az üzembe helyezés akár 10 percet vagy többet is igénybe vehet.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az üzembe helyezés befejezése után a következő hasonló erőforrásokat láthatja.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Üzembe helyezett erőforrások":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Firewall Manager szabályzat áttekintése](policy-overview.md)
