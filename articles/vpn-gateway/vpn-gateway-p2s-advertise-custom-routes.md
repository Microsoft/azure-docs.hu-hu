---
title: 'Azure VPN Gateway: egyéni útvonalak meghirdetése P2S VPN-ügyfelek számára'
description: Az egyéni útvonalaknak a pont-hely típusú ügyfelekhez való reklámozásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89392391"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Egyéni útvonalak hirdetése P2S VPN-ügyfelek számára

Érdemes lehet egyéni útvonalakat reklámozni az összes pont – hely VPN-ügyfélre. Ha például engedélyezte a tárolási végpontokat a VNet, és azt szeretné, hogy a távoli felhasználók hozzáférhessenek ezekhez a Storage-fiókokhoz a VPN-kapcsolaton keresztül. Meghirdetheti a tároló végpontjának IP-címét az összes távoli felhasználó számára, hogy a Storage-fiók felé irányuló forgalom a VPN-alagúton, ne pedig a nyilvános interneten legyen.

![Azure VPN Gateway többhelyes kapcsolat – példa](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Egyéni útvonalak meghirdetése

Egyéni útvonalak meghirdetéséhez használja a következőt: `Set-AzVirtualNetworkGateway cmdlet` . Az alábbi példa bemutatja, hogyan hirdetheti meg az IP-címet a [contoso Storage-fiókok tábláiban](https://contoso.table.core.windows.net).

1. Pingelje a *contoso.table.Core.Windows.net* , és jegyezze fel az IP-címet. Például:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. A következő PowerShell-parancsok futtatása:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Több egyéni útvonal hozzáadásához használjon vesszőt és szóközöket a címek elválasztásához. Például:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Egyéni útvonalak megtekintése

Az alábbi példa az egyéni útvonalak megtekintésére használható:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Egyéni útvonalak törlése

Az alábbi példa használatával törölheti az egyéni útvonalakat:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Következő lépések

További P2S-útválasztási információk: [Tudnivalók a pont – hely útválasztásról](vpn-gateway-about-point-to-site-routing.md).
