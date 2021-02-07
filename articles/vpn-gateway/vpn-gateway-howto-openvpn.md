---
title: Az OpenVPN konfigurálása az Azure VPN Gateway
description: Megtudhatja, hogyan használhatja a PowerShellt az OpenVPN protokoll Azure-VPN Gateway való engedélyezéséhez pont – hely környezet esetén.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 34f24b8fbdb28e1b1f73e9db428c510d3f4661ce
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804843"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Az OpenVPN konfigurálása az Azure pont – hely kapcsolathoz VPN Gateway

Ebből a cikkből megtudhatja, hogyan állíthatja be az **OpenVPN® protokollt** az Azure VPN Gateway-on. Használhatja a portált vagy a PowerShell-utasításokat is.

## <a name="prerequisites"></a>Előfeltételek

* A cikk feltételezi, hogy már rendelkezik egy működő pont – hely környezettel. Ha nem, hozzon létre egyet az alábbi módszerek egyikének használatával.

  * [Portál – pont – hely létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – pont – hely létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)

* Ellenőrizze, hogy a VPN-átjáró nem használja-e az alapszintű SKU-t. Az OpenVPN esetében az alapszintű SKU nem támogatott.

## <a name="portal"></a>Portál

1. A portálon navigáljon a **virtuális hálózati átjáró-> pont – hely konfigurációhoz**.
1. Az **alagút típusa** beállításnál válassza az **OpenVPN (SSL)** vagy a **IKEV2 és az OpenVPN (SSL)** lehetőséget a legördülő listából.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Válassza ki az OpenVPN SSL elemet a legördülő listából":::
1. Mentse a módosításokat, és folytassa a **következő lépésekkel**.

Az OpenVPN engedélyezése az átjárón.

1. Engedélyezze az OpenVPN-t az átjárón a következő példa használatával:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Folytassa a **következő lépésekkel**.

## <a name="next-steps"></a>Következő lépések

Az OpenVPN-ügyfelek konfigurálásával kapcsolatban lásd: az [OpenVPN-ügyfelek konfigurálása](vpn-gateway-howto-openvpn-clients.md).

**Az "OpenVPN" az OpenVPN Inc védjegye.**
