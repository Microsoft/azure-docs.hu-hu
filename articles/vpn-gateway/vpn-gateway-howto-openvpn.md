---
title: Az OpenVPN konfigurálása az Azure VPN Gateway
description: Megtudhatja, hogyan engedélyezheti a pont – hely környezet számára az OpenVPN protokollt az Azure VPN Gatewayban.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584040"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Az OpenVPN konfigurálása pont – hely típusú VPN-átjárók számára

Ebből a cikkből megtudhatja, hogyan állíthatja be az **OpenVPN® protokollt** az Azure VPN Gateway-on. Használhatja a portált vagy a PowerShell-utasításokat is.

## <a name="prerequisites"></a>Előfeltételek

* A cikk feltételezi, hogy már rendelkezik egy működő pont – hely környezettel. Ha nem, hozzon létre egyet az alábbi módszerek egyikének használatával.

  * [Portál – pont – hely létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – pont – hely létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)

* Ellenőrizze, hogy a VPN-átjáró nem használja-e az alapszintű SKU-t. Az OpenVPN esetében az alapszintű SKU nem támogatott.

## <a name="portal"></a>Portál

1. A portálon navigáljon a **virtuális hálózati átjáró-> pont – hely konfigurációhoz**.
1. Az **alagút típusa** beállításnál válassza az **OpenVPN (SSL)** lehetőséget a legördülő listából.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Válassza ki az OpenVPN SSL elemet a legördülő listából":::
1. Mentse a módosításokat, és folytassa a **következő lépésekkel**.

## <a name="powershell"></a>PowerShell

1. Engedélyezze az OpenVPN-t az átjárón a következő példa használatával:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Folytassa a **következő lépésekkel**.

## <a name="next-steps"></a>Következő lépések

Az OpenVPN-ügyfelek konfigurálásával kapcsolatban lásd: az [OpenVPN-ügyfelek konfigurálása](vpn-gateway-howto-openvpn-clients.md).

**Az "OpenVPN" az OpenVPN Inc védjegye.**
