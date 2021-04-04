---
title: 'Rövid útmutató: Azure WAF v2 létrehozása Application Gateway-Azure Resource Manager sablonhoz'
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre egy webalkalmazási tűzfal v2-t az Application Gateway Azure-on a Azure Resource Manager-alapú Gyorsindítás sablonnal (ARM-sablon).
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091321"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Rövid útmutató: Azure WAF v2 létrehozása Application Gateway ARM-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hoz létre egy Azure webalkalmazási tűzfal v2-et a Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon létrehoz egy egyszerű webalkalmazási tűzfal v2-et az Azure Application Gateway-on. Ez magában foglalja a nyilvános IP-cím előtérbeli IP-címét, a HTTP-beállításokat, a 80-es porton alapszintű figyelővel rendelkező szabályt, valamint egy háttér-készletet. A rendszer létrehoz egy egyéni szabállyal rendelkező WAF-házirendet, amely letiltja a háttér-készlet felé irányuló forgalmat egy IP-cím egyeztetési típusa alapján.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/) közül származik.

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses) : egy az Application Gateway számára, és kettő a virtuális gépek számára.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : két virtuális gép
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : kettő a virtuális gépekhez
- [**Microsoft. számítás/virtualMachine/bővítmények**](/azure/templates/microsoft.compute/virtualmachines/extensions) : az IIS és a weblapok konfigurálásához

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Application Gateway-t, a hálózati infrastruktúrát és két virtuális gépet az IIS-t futtató háttér-készletben.

   [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot.
3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás** lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Annak ellenére, hogy az IIS nem szükséges az Application Gateway létrehozásához, a rendszer telepíti a háttér-kiszolgálókra, hogy ellenőrizze, hogy az Azure sikeresen létrehozott-e WAF v2-et az Application Gateway-ben.

Az IIS használata az Application Gateway teszteléséhez:

1. Keresse meg az Application Gateway nyilvános IP-címét az **Áttekintés** oldalon. ![ Az Application Gateway nyilvános IP-címének rögzítése ](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) vagy a **minden erőforrás** lehetőség kiválasztásával megadhatja a *myAGPublicIPAddress* kifejezést a keresőmezőbe, majd kiválaszthatja azt a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **Áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába az IP-cím megkereséséhez.
3. Keresse meg a választ. Egy **403 Tiltott** válasz ellenőrzi, hogy a WAF létrehozása sikeres volt-e, és blokkolja-e a háttér-készlet kapcsolatait.
4. Módosítsa az egyéni szabályt úgy, hogy **engedélyezze a forgalmat**.
  Futtassa az alábbi Azure PowerShell parancsfájlt, és cserélje le az erőforráscsoport nevét:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Frissítse a böngészőt többször, és a myVM1 és a myVM2 kapcsolatait is látnia kell.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az Application Gatewayt és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Application Gateway létrehozása webalkalmazási tűzfallal a Azure Portal használatával](application-gateway-web-application-firewall-portal.md)