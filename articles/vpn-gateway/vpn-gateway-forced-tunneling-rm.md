---
title: Kényszerített bújtatás konfigurálása a helyek közötti kapcsolatokhoz
description: Az internethez kötött forgalom átirányítása (kényszerített) a helyszíni helyre.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/07/2020
ms.author: cherylmc
ms.openlocfilehash: c12297019b49d7b3cb644ae9c7a904e4ca697f0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96855039"
---
# <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása

A kényszerített bújtatással a helyek közötti VPN-alagúton keresztül az internetre irányuló összes forgalom visszairányítható (kényszeríthető) a helyszíni helyre vizsgálat és naplózás céljából. Ez kritikus fontosságú biztonsági követelmény a legtöbb vállalati informatikai házirend számára. Ha nem konfigurálja a kényszerített bújtatást, az Azure-beli virtuális gépekről érkező, az interneten keresztül továbbított forgalom az Azure-beli hálózati infrastruktúráról közvetlenül az interneten keresztül jut el, és nem teszi lehetővé a forgalom vizsgálatát vagy naplózását. A jogosulatlan internet-hozzáférés az adatokhoz való illetéktelen hozzáférést vagy más típusú biztonsági szabálysértéseket eredményezhet.

A kényszerített bújtatást Azure PowerShell használatával lehet konfigurálni. Nem konfigurálható a Azure Portal használatával. Ebből a cikkből megtudhatja, hogyan konfigurálhat kényszerített bújtatást a Resource Manager-alapú üzemi modell használatával létrehozott virtuális hálózatokhoz. Ha a klasszikus üzemi modell kényszerített bújtatását kívánja konfigurálni, tekintse meg a következőt: [kényszerített bújtatás – klasszikus](vpn-gateway-about-forced-tunneling.md).

## <a name="about-forced-tunneling"></a>A kényszerített bújtatás ismertetése

A következő ábra a kényszerített bújtatás működését mutatja be.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="A diagramon a kényszerített bújtatás látható.":::

Ebben a példában az előtérbeli alhálózat nem kényszerített bújtatást. Az előtér-alhálózaton lévő munkaterhelések továbbra is elfogadják, és közvetlenül az internetről válaszolnak az ügyfelek kéréseire. A középső réteg és a háttérbeli alhálózatok kényszerített bújtatással vannak elválasztva. A két alhálózatról az internetre irányuló kimenő kapcsolatokat a rendszer a helyek közötti (S2S) VPN-alagutak egyikével kényszeríti vagy átirányítja egy helyszíni helyre.

Ez lehetővé teszi az Azure-beli virtuális gépek vagy felhőalapú szolgáltatások internet-hozzáférésének korlátozását és vizsgálatát, miközben továbbra is engedélyezni kell a többrétegű szolgáltatási architektúrát. Ha a virtuális hálózatokban nincsenek internetkapcsolattal rendelkező munkaterhelések, a teljes virtuális hálózatokra kényszerített bújtatás is alkalmazható.

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások

Az Azure-ban a kényszerített bújtatás az egyéni, felhasználó által megadott útvonalak használatával van konfigurálva. A helyszíni helyre irányuló forgalom átirányítása az Azure VPN Gateway alapértelmezett útvonala. A felhasználó által megadott útválasztással és virtuális hálózatokkal kapcsolatos további információkért lásd: [egyéni felhasználó által definiált útvonalak](../virtual-network/virtual-networks-udr-overview.md#user-defined).

* Minden egyes virtuális hálózati alhálózat beépített, rendszer-útválasztási táblázattal rendelkezik. A rendszerútválasztási táblázat a következő három útvonal-csoporttal rendelkezik:
  
  * **Helyi VNet útvonalak:** Közvetlenül az azonos virtuális hálózatban található cél virtuális gépekre.
  * Helyszíni **útvonalak:** Az Azure VPN Gateway-hez.
  * **Alapértelmezett útvonal:** Közvetlenül az internethez. Az előző két útvonal által nem jelzett magánhálózati IP-címekre irányuló csomagokat a rendszer elveti.
* Ez az eljárás felhasználó által megadott útvonalakat (UDR) használ egy útválasztási tábla létrehozásához az alapértelmezett útvonal hozzáadásához, majd az útválasztási táblázatot a VNet-alhálózathoz társítja, hogy engedélyezze a kényszerített bújtatást ezeken az alhálózatokon.
* A kényszerített bújtatást olyan VNet kell társítani, amely egy Route-alapú VPN-átjáróval rendelkezik. Az "alapértelmezett hely" beállítást kell beállítani a virtuális hálózathoz csatlakoztatott telephelyi helyi telephelyek között. Emellett a helyszíni VPN-eszközt a 0.0.0.0/0 protokollal kell konfigurálni a forgalmi választóként. 
* A ExpressRoute kényszerített bújtatása nem ezen a mechanizmuson keresztül van konfigurálva, hanem a ExpressRoute BGP-társítási munkameneteken keresztüli alapértelmezett útvonal hirdetésével van engedélyezve. További információkért tekintse meg a [ExpressRoute dokumentációját](https://azure.microsoft.com/documentation/services/expressroute/).
* Ha a VPN Gateway és a ExpressRoute átjáró ugyanazon a VNet van telepítve, a felhasználó által megadott útvonalakat (UDR) már nincs szükség, mert a ExpressRoute-átjáró az "alapértelmezett hely" kifejezést hirdeti meg a VNet.

## <a name="configuration-overview"></a>Konfiguráció áttekintése

Az alábbi eljárás segítséget nyújt egy erőforráscsoport és egy VNet létrehozásához. Ezután létre kell hoznia egy VPN-átjárót, és konfigurálnia kell a kényszerített bújtatást. Ebben az eljárásban a "többrétegű VNet" virtuális hálózat három alhálózattal rendelkezik: "frontend", "egy midtier" és "háttér", négy telephelyi kapcsolattal: "DefaultSiteHQ" és három ág.

Az eljárás lépései a "DefaultSiteHQ" értéket adja meg a kényszerített bújtatáshoz tartozó alapértelmezett helyként, és az "egy midtier" és a "háttér" alhálózatokat kényszerített bújtatás használatára konfigurálja.

## <a name="before-you-begin"></a><a name="before"></a>Kezdés előtt

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

> [!IMPORTANT]
> A PowerShell-parancsmagok legújabb verziójának telepítése szükséges. Ellenkező esetben az egyes parancsmagok futtatásakor érvényesítési hibák léphetnek fel.
>
>

### <a name="to-sign-in"></a>Bejelentkezés

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása

> [!NOTE]
> Előfordulhat, hogy a következő figyelmeztetések láthatók: "a parancsmag kimeneti objektumának típusa módosítva lesz egy jövőbeli kiadásban". Ez a várt viselkedés, és nyugodtan figyelmen kívül hagyhatja ezeket a figyelmeztetéseket.
>
>


1. Hozzon létre egy erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Hozzon létre egy virtuális hálózatot, és határozza meg az alhálózatokat.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Hozza létre a helyi hálózati átjárókat.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Hozza létre az útválasztási táblázatot és az útválasztási szabályt.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Rendelje hozzá az útválasztási táblázatot a egy midtier és a háttérbeli alhálózatokhoz.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Hozza létre a virtuális hálózati átjárót. Ez a lépés hosszabb időt vesz igénybe, esetenként 45 perc vagy több, mert az átjárót hozza létre és konfigurálja. Ha a Gatewaysku paraméterben értékkel kapcsolatos ValidateSet hibák jelennek meg, ellenőrizze, hogy telepítette-e a [PowerShell-parancsmagok legújabb verzióját](#before). A PowerShell-parancsmagok legújabb verziója a legújabb Gateway SKU-ok új érvényesített értékeit tartalmazza.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Rendeljen hozzá egy alapértelmezett helyet a virtuális hálózati átjáróhoz. A **-GatewayDefaultSite** a parancsmag paraméter, amely lehetővé teszi a kényszerített útválasztási konfiguráció működését, ezért ügyeljen arra, hogy megfelelően konfigurálja ezt a beállítást. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Hozza létre a helyek közötti VPN-kapcsolatokat.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
