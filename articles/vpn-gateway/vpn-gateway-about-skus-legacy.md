---
title: Örökölt Azure Virtual Network VPN Gateway SKU-i
description: A régi virtuális hálózati átjáró SKU-i használata; Alapszintű, standard és HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84687786"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>A Virtual Network Gateway SKU-i (örökölt SKU) használata

Ez a cikk az örökölt (régi) virtuális hálózati átjáró SKU-ról tartalmaz információkat. Az örökölt SKU-ket továbbra is a már létrehozott VPN-átjárók üzembe helyezési modelljei is működnek. A klasszikus VPN-átjárók továbbra is a régi SKU-ket használják a meglévő átjárók és az új átjárók esetében. Új Resource Manager VPN Gateway-átjárók létrehozásakor használja az új átjárók SKU-t. További információ az új SKU-ról: [about VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Az örökölt átjáró díjszabását a [ExpressRoute díjszabása lapon](https://azure.microsoft.com/pricing/details/expressroute)található **Virtual Network átjárók** című szakaszban tekintheti meg.

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>A termékváltozat becsült összesített átviteli sebessége

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Az SKU és a VPN típusa által támogatott konfigurációk

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Átjáró átméretezése

Az átjárót átméretezheti egy olyan átjáró SKU-ra, amely ugyanazon SKU-családon belül található. Ha például standard SKU-t használ, átméretezheti a HighPerformance SKU-ra. A VPN-átjárót azonban nem lehet átméretezni a régi SKU-és az új SKU-család között. Például nem mehet át standard SKU-ról egy VpnGw2 SKU-ra vagy egy alapszintű SKU-VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Ha át szeretné méretezni a Resource Manager-alapú üzemi modell átjáróját a PowerShell használatával, használja a következő parancsot:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Az átjárókat a Azure Portal is átméretezheti.

### <a name="classic"></a><a name="classicresize"></a>Klasszikus

A klasszikus üzemi modell átjárójának átméretezéséhez a Service Management PowerShell-parancsmagokat kell használnia. Használja az alábbi parancsot:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Váltás az új átjárós SKU-ra

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Következő lépések

További információ az átjárók új SKU-ról: [átjáró SKU](vpn-gateway-about-vpngateways.md#gwsku)-i.

További információ a konfigurációs beállításokról: [Tudnivalók a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).
