---
title: 'Route-alapú VPN-átjáró létrehozása: portál'
titleSuffix: Azure VPN Gateway
description: A Azure Portal használatával gyorsan létrehozhat egy Route-alapú Azure VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolathoz vagy a virtuális hálózatok összekapcsolásához.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e24473380fe4d0837518f1616df52621089ff2a5
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92328981"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Route-alapú VPN-átjáró létrehozása a Azure Portal használatával

Ez a cikk segítséget nyújt egy Route-alapú Azure VPN-átjáró gyors létrehozásához a Azure Portal használatával.  VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolat létrehozásakor használ a rendszer. VPN-átjárót is használhat a virtuális hálózatok összekapcsolásához. 

A cikkben szereplő lépések egy VNet, egy alhálózatot, egy átjáró-alhálózatot és egy Route-alapú VPN-átjárót (virtuális hálózati átjárót) hoznak létre. Miután az átjáró létrehozása befejeződött, létrehozhat kapcsolatokat. Ezeknek a lépéseknek Azure-előfizetésre van szükségük. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Virtuális hálózat létrehozása

[!INCLUDE [Cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Az átjáró konfigurálása és létrehozása

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Az alapszintű átjáró SKU nem támogatja a IKEv2 vagy a RADIUS-hitelesítést. Ha azt tervezi, hogy a Mac-ügyfelek csatlakoznak a virtuális hálózathoz, ne használja az alapszintű SKU-t.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>A VPN-átjáró megtekintése

1. Az átjáró létrehozása után navigáljon a VNet1 a portálon. A VPN-átjáró csatlakoztatott eszközként jelenik meg az Áttekintés oldalon.

   ![Csatlakoztatott eszközök](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Csatlakoztatott eszközök")

2. További információk megtekintéséhez az eszközök listájában kattintson a **VNet1GW** elemre.

   ![VPN-átjáró megtekintése](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN-átjáró megtekintése")

## <a name="next-steps"></a>Következő lépések

Miután az átjáró elkészült, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy hozzon létre kapcsolatot a virtuális hálózat és a helyszíni hely között.

> [!div class="nextstepaction"]
> [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Pont–hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Kapcsolatok létrehozása másik VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
