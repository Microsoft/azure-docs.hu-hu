---
title: 'Oktatóanyag – VPN Gateway létrehozása és kezelése: Azure Portal'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre, helyezhet üzembe és kezelhet Azure-VPN Gateway a portál használatával
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 820482a268af038737557c517ccda086cd65d943
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880559"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Oktatóanyag: VPN Gateway létrehozása és kezelése Azure Portal használatával

Az Azure VPN-átjárók helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ez az oktatóanyag az Azure VPN-átjárók üzembe helyezésének alapvető elemeit ismerteti, például a VPN-átjárók létrehozását és felügyeletét. Az [Azure CLI](create-routebased-vpn-gateway-cli.md) vagy a [Azure PowerShell](create-routebased-vpn-gateway-powershell.md)használatával is létrehozhat egy átjárót.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * VPN-átjáró létrehozása
> * Az átjáró nyilvános IP-címének megtekintése
> * VPN-átjáró átméretezése (SKU átméretezése)
> * VPN-átjáró alaphelyzetbe állítása

Az alábbi diagram az oktatóanyag keretében létrehozott virtuális hálózatot és VPN-átjárót mutatja.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="VNet és VPN Gateway diagram":::

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Virtuális hálózat létrehozása

Hozzon létre egy VNet a következő értékek használatával:

* **Erőforráscsoport:** TestRG1
* **Név:** VNet1
* **Régió:** (USA) USA keleti régiója
* **IPv4-címterület:** 10.1.0.0/16
* **Alhálózat neve:** FrontEnd
* **Alhálózat címterület:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>VPN-átjáró létrehozása

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

Hozzon létre egy virtuális hálózati átjárót a következő értékek használatával:

* **Név:** VNet1GW
* **Régió:** USA keleti régiója
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **SKU:** VpnGw1
* **Generáció:** Generation1
* **Virtuális hálózat:** VNet1
* **Átjáró-alhálózati címtartomány:** 10.1.255.0/27
* **Nyilvános IP-cím:** Új létrehozása
* **Nyilvános IP-cím neve:** VNet1GWpip
* **Aktív-aktív üzemmód engedélyezése:** Tiltva
* A **BGP konfigurálása:** Tiltva

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>A nyilvános IP-cím megtekintése

Az átjáró nyilvános IP-címét az átjáró **Áttekintés** oldalán tekintheti meg.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Áttekintő oldal":::

Ha további információkat szeretne látni a nyilvános IP-cím objektumról, kattintson a **nyilvános IP-cím** melletti név/IP-cím hivatkozásra.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Átjáró SKU-jának átméretezése

Az átméretezésre és az átjáró SKU-jának módosítására vonatkozó konkrét szabályok vonatkoznak. Ebben a szakaszban átméretezi az SKU-t. További információ: [átjáró beállításai – az SKU-i átméretezése és módosítása](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Átjáró alaphelyzetbe állítása

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, vagy folytassa a következő oktatóanyaggal, törölje ezeket az erőforrásokat a következő lépésekkel:

1. Adja meg az erőforráscsoport nevét a portál tetején található **keresőmezőbe** , és válassza ki a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg az erőforráscsoport **nevét, írja be az erőforráscsoport nevét** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha a VPN-átjáróval rendelkezik, megadhatja a kapcsolatokat. Az alábbi cikkek segítséget nyújtanak a leggyakoribb konfigurációk létrehozásához:

> [!div class="nextstepaction"]
> [Helyek közötti VPN-kapcsolatok](./tutorial-site-to-site-portal.md)

> [!div class="nextstepaction"]
> [Pont – hely típusú VPN-kapcsolatok](vpn-gateway-howto-point-to-site-resource-manager-portal.md)