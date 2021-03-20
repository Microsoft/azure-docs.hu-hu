---
title: Azure PowerShell parancsfájl-minta – egyéni WAF-szabályok létrehozása
description: Azure PowerShell parancsfájl-minta – webalkalmazási tűzfal egyéni szabályainak létrehozása
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 312f052671036d8153dd19fcf4e559e825fd8464
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396991"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>Webalkalmazási tűzfal (WAF) egyéni szabályainak létrehozása a Azure PowerShell

Ez a szkript létrehoz egy Application Gateway webalkalmazási tűzfalat, amely egyéni szabályokat használ. Az egyéni szabály blokkolja a forgalmat, ha a kérelem fejléce User-Agent *evilbot* tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Ha a Azure PowerShell helyi telepítését és használatát választja, akkor ehhez a parancsfájlhoz a Azure PowerShell modul 2.1.0 vagy újabb verziójára van szükség.

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.
2. Az Azure-beli kapcsolatok létrehozásához futtassa a parancsot `Connect-AzAccount` .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal eltávolítható az erőforráscsoport, az alkalmazásátjáró és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Létrehozza az alhálózatkonfigurációt. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehozza a virtuális hálózatot az alhálózatkonfigurációk használatával. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [Új – AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Az alhálózatot az alkalmazásátjáróval társító konfigurációt hozza létre. |
| [Új – AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | A nyilvános IP-címet az alkalmazásátjáróhoz hozzárendelő konfigurációt hozza létre. |
| [Új – AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hozzárendeli az alkalmazásátjáró elérésére használt portot. |
| [Új – AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Háttérkészletet hoz létre az alkalmazásátjáróhoz. |
| [Új – AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfigurálja a háttérkészlet beállításait. |
| [Új – AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Figyelőt hoz létre. |
| [Új – AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Útválasztási szabályt hoz létre. |
| [Új – AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Az alkalmazásátjáró szintjét és kapacitását határozza meg. |
| [Új – AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Egy alkalmazásátjárót hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |
|[Új – AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Létrehoz egy automatikus skálázási konfigurációt a Application Gateway számára.|
|[Új – AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Létrehoz egy Match változót a tűzfal feltételéhez.|
|[Új – AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Létrehoz egy egyeztetési feltételt az egyéni szabályhoz.|
|[Új – AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Létrehoz egy új egyéni szabályt az Application gateway tűzfal házirendjéhez.|
|[Új – AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Létrehoz egy Application gateway-tűzfal házirendet.|
|[Új – AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Létrehoz egy WAF-konfigurációt az Application Gateway számára.|

## <a name="next-steps"></a>Következő lépések

- További információ az egyéni WAF-szabályokról: [webalkalmazási tűzfal egyéni szabályai](../../web-application-firewall/ag/custom-waf-rules-overview.md)
- Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).
- Az alkalmazásátjárókkal használható további PowerShell-példaszkripteket [Az Application Gateway dokumentációjában](../powershell-samples.md) találhat.