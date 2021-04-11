---
title: 'Azure VPN Gateway: pont – hely VPN-munkamenetek kezelése'
description: Ez a cikk segít megtekinteni és leválasztani a pont – hely VPN-munkameneteket.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: b55fe0bf404ecb8a81e3fe1975dfa9f5ba5dfb06
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103351"
---
# <a name="point-to-site-vpn-session-management"></a>Pont – hely típusú VPN-munkamenetek kezelése

Az Azure-beli virtuális hálózati átjárók egyszerűen megtekinthetik és leválasztják az aktuális pont – hely típusú VPN-munkameneteket. Ez a cikk segítséget nyújt az aktuális munkamenetek megtekintéséhez és leválasztásához.

>[!NOTE]
>A munkamenet állapota 5 percenként frissül. Nem azonnal frissül.
>

## <a name="portal"></a>Portál

Munkamenet megtekintése és leválasztása a portálon:

1. Navigáljon a VPN-átjáróhoz.
1. A **figyelés** szakaszban válassza a **pont – hely munkamenetek** lehetőséget.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Példa portálra":::
1. Az összes aktuális munkamenetet megtekintheti a Windowpane.
1. Válassza a **"..."** lehetőséget a leválasztani kívánt munkamenethez, majd válassza a **Leválasztás** lehetőséget.

Jelenleg nem használhatja ezt a funkciót a portálon a VpnGw4 és a VpnGw5 SKU-hoz. Ha rendelkezik ezekkel az átjárókkal, használja a következő szakaszban leírt PowerShell-metódust.

## <a name="powershell"></a>PowerShell

Munkamenet megtekintése és leválasztása a PowerShell használatával:

1. Futtassa az alábbi PowerShell-parancsot az aktív munkamenetek listázásához:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Másolja ki a leválasztani kívánt munkamenet **VpnConnectionId** .

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell-példa":::
1. A munkamenet leválasztásához futtassa a következő parancsot:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Következő lépések

További információ a pont – hely kapcsolatokról: [Tudnivalók a pont – hely VPN-ről](point-to-site-about.md).
