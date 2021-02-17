---
title: 'Rövid útmutató: hozzon létre egy Azure Firewall és egy tűzfal-házirendet – Resource Manager-sablon'
description: Ebben a rövid útmutatóban egy Azure Firewall és egy tűzfal-házirendet telepít.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 26d7336d60449db11122c9921a3a59807bd82911
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562188"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Rövid útmutató: hozzon létre egy Azure Firewall és egy tűzfal-házirendet – ARM-sablon

Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hoz létre egy Azure Firewall és egy tűzfal-házirendet. A tűzfalszabályok olyan alkalmazási szabállyal rendelkeznek, amely lehetővé teszi a csatlakozást `www.microsoft.com` és egy olyan szabályt, amely lehetővé teszi, hogy a Windows Update kapcsolatokat a **windowsupdate** FQDN címke használatával engedélyezze. A hálózati szabályok lehetővé teszik az UDP-kapcsolatok elérését egy időkiszolgálónál a 13.86.101.172-on.

Emellett az IP-csoportok a szabályokban használatosak a **forrás** IP-címek definiálásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

További információ a Azure Firewall Managerrel kapcsolatban: [Mi az a Azure Firewall Manager?](overview.md).

További információ a Azure Firewallről: [Mi az Azure Firewall?](../firewall/overview.md)

Az IP-csoportokkal kapcsolatos további információkért lásd: [IP-csoportok a Azure Firewallban](../firewall/ip-groups.md).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon egy biztonságos virtuális hubot hoz létre a Azure Firewall Manager használatával, valamint a forgatókönyv támogatásához szükséges erőforrásokat.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Azure Firewall, egy virtuális WAN-t és egy virtuális hubot, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. A portálon, a **tűzfal létrehozása és a FirewallPolicy a szabályok és Ipgroups** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: válasszon a meglévő előfizetések közül.
   - Erőforráscsoport: válasszon a meglévő erőforráscsoportok közül, vagy válassza az **új létrehozása** lehetőséget, majd kattintson **az OK gombra**.
   - Régió: válasszon régiót.
   - Tűzfal neve: írja be a tűzfal nevét.

3. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az üzembe helyezés befejezése után a következő hasonló erőforrások jelennek meg.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Üzembe helyezett erőforrások":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Firewall Manager-házirend áttekintése](policy-overview.md)
