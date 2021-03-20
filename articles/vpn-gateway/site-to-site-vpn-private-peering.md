---
title: Helyek közötti VPN-kapcsolatok ExpressRoute privát kapcsolaton keresztül
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti a helyek közötti VPN-t a ExpressRoute privát kapcsolaton keresztül a forgalom titkosítása érdekében.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880525"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Helyek közötti VPN-kapcsolat konfigurálása ExpressRoute-alapú privát kapcsolatok használatával

A helyek közötti VPN-t egy RFC 1918 IP-cím használatával konfigurálhatja egy virtuális hálózati átjáróra egy ExpressRoute privát társon keresztül. Ez a konfiguráció a következő előnyöket biztosítja:

* A privát kapcsolaton keresztüli forgalom titkosítva van.

* A virtuális hálózati átjáróhoz csatlakozó pont – hely felhasználók a helyszíni erőforrások eléréséhez a ExpressRoute (a helyek közötti alagúton keresztül) használhatják.

>[!NOTE]
>Ez a funkció csak a zóna redundáns átjárók esetében támogatott. Például: VpnGw1AZ, VpnGw2AZ stb.
>

A konfiguráció elvégzéséhez győződjön meg arról, hogy megfelel a következő előfeltételeknek:

* Rendelkezik egy működő ExpressRoute-áramkörrel, amely ahhoz a VNet van csatolva, amelyhez a VPN-átjárót (vagy a-t) létrehozták.

* A ExpressRoute áramkörön keresztül elérheti az erőforrásokat a RFC1918 (magánhálózati) IP-VNet keresztül.

## <a name="routing"></a><a name="routing"></a>Útválasztás

Az **1. ábrán** egy példa látható a VPN-kapcsolatra a privát ExpressRoute. Ebben a példában egy hálózatot lát a helyszíni hálózaton belül, amely az Azure hub VPN Gateway-hez csatlakozik a ExpressRoute-alapú privát kapcsolaton keresztül. Ennek a konfigurációnak a fontos aspektusa a helyszíni hálózatok és az Azure közötti útválasztás a ExpressRoute és a VPN elérési útjain keresztül.

1. ábra

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="1. ábra":::

A kapcsolat létrehozása egyszerű:

1. Hozzon létre ExpressRoute kapcsolatot egy ExpressRoute-áramkörrel és egy privát partneri kapcsolattal.

1. Hozza létre a VPN-kapcsolatot a cikkben ismertetett lépések alapján.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Helyszíni hálózatokból az Azure-ba irányuló forgalom

A helyszíni hálózatokról az Azure-ba irányuló adatforgalom esetén az Azure-előtagokat a ExpressRoute privát társ-összevonási BGP és a VPN BGP használatával hirdetjük meg. Az eredmény két hálózati útvonal (elérési út) az Azure felé a helyszíni hálózatokból:

• Egy hálózati útvonal az IPsec által védett útvonalon.

• Az egyik hálózati útvonal közvetlenül a ExpressRoute-en keresztül, IPsec-védelem nélkül.

Ha titkosítást szeretne alkalmazni a kommunikációra, meg kell győződnie arról, hogy az 1. **ábrán** látható VPN-kapcsolattal rendelkező hálózat esetén a helyszíni VPN-átjárón keresztüli Azure-útvonalak előnyben részesítettek a közvetlen ExpressRoute útvonalon.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Az Azure-ból a helyszíni hálózatokra irányuló forgalom

Ugyanez a követelmény vonatkozik az Azure-ból a helyszíni hálózatokra érkező forgalomra is. Ha biztosítani szeretné, hogy az IPsec elérési útja előnyben legyen a közvetlen ExpressRoute útvonalon (IPsec nélkül), két lehetőség közül választhat:

• A VPN **-kapcsolattal rendelkező hálózathoz tartozó VPN BGP-munkamenetben konkrétabb előtagokat Hirdessen**. Meghirdetheti a VPN-kapcsolattal rendelkező hálózatot a ExpressRoute privát kapcsolaton keresztül, majd a VPN BGP-munkamenetben meghatározott tartományokat is. Például Hirdessen 10.0.0.0/16 over ExpressRoute, és 10.0.1.0/24 VPN-en keresztül.

• **A VPN-és ExpressRoute különálló előtagjainak reklámozása**. Ha a VPN-kapcsolattal rendelkező hálózati tartományok más ExpressRoute csatlakoztatott hálózatokból vannak kiválasztva, akkor a VPN-és ExpressRoute BGP-munkamenetekben meghirdetheti az előtagokat. Például Hirdessen 10.0.0.0/24 over ExpressRoute, és 10.0.1.0/24 VPN-en keresztül.

Mindkét példában az Azure a VPN-kapcsolaton keresztül küldi el a forgalmat a 10.0.1.0/24-re, nem pedig közvetlenül a VPN-védelem nélküli ExpressRoute.

>[!Warning]
>Ha ugyanazokat az előtagokat hirdeti meg mind a ExpressRoute, mind a VPN-kapcsolaton keresztül, >Azure közvetlenül VPN-védelem nélkül fogja használni a ExpressRoute útvonalat.
>

## <a name="portal-steps"></a><a name="portal"></a>A portál lépései

1. Helyek közötti kapcsolat konfigurálása. A lépéseket a helyek közötti [konfiguráció](./tutorial-site-to-site-portal.md) című cikkben találja. Ügyeljen arra, hogy az átjáróhoz egy zóna-redundáns átjáró SKU-t válasszon. 

   A redundáns SKU-ket az SKU végén "AZ" értékkel kell elvégezni. Például: **VpnGw1AZ**. Zóna – a redundáns átjárók csak olyan régiókban érhetők el, ahol elérhető a rendelkezésre állási zóna szolgáltatás. További információ a rendelkezésre állási zónákat támogató régiókkal kapcsolatban: a [rendelkezésre állási zónákat támogató régiók](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Átjáró magánhálózati IP-címei":::
1. Engedélyezze a magánhálózati IP-címeket az átjárón. Válassza a **konfiguráció** lehetőséget, majd állítsa be az **átjáró magánhálózati IP-címei** **beállítást.** A módosítások mentéséhez válassza a **Mentés** gombot.
1. Az **Áttekintés** lapon válassza a **továbbiak** lehetőséget a magánhálózati IP-cím megtekintéséhez. Jegyezze fel ezt az információt, hogy később a konfigurációs lépések során is használni lehessen.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Áttekintő oldal" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Ha engedélyezni szeretné az **Azure magánhálózati IP-cím használatát** a kapcsolatban, válassza a  **Konfigurálás** lehetőséget. Állítsa be az **Azure Private IP-cím használata** **engedélyezve** beállítást, majd kattintson a **Mentés** gombra.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Átjáró magánhálózati IP-címei – engedélyezve":::
1. A tűzfalon Pingelje a 3. lépésben írt privát IP-címet. A magánhálózati IP-címet elérhetővé kell tennie a ExpressRoute privát kapcsolaton keresztül.
1. Ezt a magánhálózati IP-címet használja távoli IP-ként a helyszíni tűzfalon, hogy a helyek közötti alagutat létrehozza a ExpressRoute privát társának.

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell-lépések

1. Helyek közötti kapcsolat konfigurálása. A lépésekért tekintse meg a [helyek közötti VPN konfigurálása](./tutorial-site-to-site-portal.md) című cikket. Ügyeljen arra, hogy az átjáróhoz egy zóna-redundáns átjáró SKU-t válasszon. A redundáns SKU-ket az SKU végén "AZ" értékkel kell elvégezni. Például: VpnGw1AZ.
1. Állítsa be úgy a jelzőt, hogy a magánhálózati IP-címet használja az átjárón a következő PowerShell-parancsok használatával:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Egy nyilvános és egy magánhálózati IP-címet kell látnia. Írja le az IP-címet a kimenet "TunnelIpAddresses" szakaszában. Ezt az információt egy későbbi lépésben fogja használni.
1. A magánhálózati IP-cím használatára a következő PowerShell-paranccsal állíthatja be a kapcsolódást:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. A tűzfalon Pingelje a 2. lépésben írt privát IP-címet. A ExpressRoute privát kapcsolaton keresztül elérhetőnek kell lennie.
1. Ezt a magánhálózati IP-címet használja távoli IP-ként a helyszíni tűzfalon, hogy a helyek közötti alagutat létrehozza a ExpressRoute privát társának.

## <a name="next-steps"></a>Következő lépések

További információ a VPN Gatewayről: [Mi az VPN Gateway?](vpn-gateway-about-vpngateways.md)