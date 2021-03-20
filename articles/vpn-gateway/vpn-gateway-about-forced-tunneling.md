---
title: 'Azure VPN Gateway: kényszerített bújtatás konfigurálása a helyek közötti kapcsolatokhoz: klasszikus'
description: Megtudhatja, hogyan konfigurálhat kényszerített bújtatást a klasszikus üzemi modell használatával létrehozott virtuális hálózatok esetében.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: af4359efb48898c12bb8ee7ffb882448b5012d19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151358"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Kényszerített bújtatás konfigurálása klasszikus üzemi modellel

A kényszerített bújtatással a helyek közötti VPN-alagúton keresztül az internetre irányuló összes forgalom visszairányítható (kényszeríthető) a helyszíni helyre vizsgálat és naplózás céljából. Ez kritikus fontosságú biztonsági követelmény a legtöbb vállalati informatikai házirend számára. A kényszerített bújtatás nélkül az Azure-beli virtuális gépekről érkező, az interneten keresztül kötött forgalom mindig az Azure hálózati infrastruktúrából kerül be az internetre, és nem teszi lehetővé a forgalom vizsgálatát vagy naplózását. A jogosulatlan internet-hozzáférés az adatokhoz való illetéktelen hozzáférést vagy más típusú biztonsági szabálysértéseket eredményezhet.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ez a cikk végigvezeti a kényszerített bújtatás konfigurálásán a klasszikus üzemi modell használatával létrehozott virtuális hálózatok esetében. A kényszerített bújtatás a PowerShell használatával konfigurálható, nem a portálon keresztül. Ha a Resource Manager-alapú üzemi modellhez kényszerített bújtatást szeretne beállítani, válassza ki a Resource Manager-cikket a következő legördülő listából:

> [!div class="op_single_selector"]
> * [Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások

A kényszerített bújtatás az Azure-ban virtuális hálózat felhasználó által megadott útvonalakon (UDR) keresztül van konfigurálva. A helyszíni helyre irányuló forgalom átirányítása az Azure VPN Gateway alapértelmezett útvonala. A következő szakasz az Azure-Virtual Network útválasztási táblázatának és útvonalának aktuális korlátozásait sorolja fel:

* Minden egyes virtuális hálózati alhálózat beépített, rendszer-útválasztási táblázattal rendelkezik. A rendszerútválasztási táblázat a következő három útvonal-csoporttal rendelkezik:

  * **Helyi VNet útvonalak:** Közvetlenül az azonos virtuális hálózatban található cél virtuális gépekre.
  * Helyszíni **útvonalak:** Az Azure VPN Gateway-hez.
  * **Alapértelmezett útvonal:** Közvetlenül az internethez. Az előző két útvonal által nem érintett magánhálózati IP-címekre irányuló csomagokat a rendszer elveti.
* A felhasználó által megadott útvonalak kiadásával létrehozhat egy útválasztási táblázatot egy alapértelmezett útvonal hozzáadásához, majd társíthatja az útválasztási táblázatot a VNet alhálózatához, hogy engedélyezze a kényszerített bújtatást ezeken az alhálózatokon.
* Az "alapértelmezett hely" beállítást kell beállítani a virtuális hálózathoz csatlakoztatott telephelyi helyi telephelyek között.
* A kényszerített bújtatást olyan VNet kell társítani, amely dinamikus útválasztási VPN-átjáróval (nem statikus átjáróval) rendelkezik.
* A ExpressRoute kényszerített bújtatása nem ezen a mechanizmuson keresztül van konfigurálva, hanem a ExpressRoute BGP-társítási munkameneteken keresztüli alapértelmezett útvonal hirdetésével van engedélyezve. További információ: [What is ExpressRoute?](../expressroute/expressroute-introduction.md).

## <a name="configuration-overview"></a>Konfiguráció áttekintése

A következő példában az előtér-alhálózat nem kényszerített bújtatással van elvégezve. Az előtér-alhálózaton lévő munkaterhelések továbbra is elfogadják, és közvetlenül az internetről válaszolnak az ügyfelek kéréseire. A középső réteg és a háttérbeli alhálózatok kényszerített bújtatással vannak elválasztva. A két alhálózatról az internetre irányuló kimenő kapcsolatokat a rendszer a S2S VPN-alagutak egyikével kényszeríti vagy átirányítja egy helyszíni helyre.

Ez lehetővé teszi az Azure-beli virtuális gépek vagy felhőalapú szolgáltatások internet-hozzáférésének korlátozását és vizsgálatát, miközben továbbra is engedélyezni kell a többrétegű szolgáltatási architektúrát. Emellett kényszerített bújtatást is alkalmazhat a teljes virtuális hálózatokra, ha a virtuális hálózatokon nincsenek internetkapcsolattal rendelkező munkaterhelések.

![Kényszerített bújtatás](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>Előfeltételek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Egy konfigurált virtuális hálózat. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása

A következő eljárás segítséget nyújt egy virtuális hálózat kényszerített bújtatásának megadásához. A konfigurációs lépések megfelelnek a VNet hálózati konfigurációs fájljának.  Ebben a példában a "többrétegű VNet" virtuális hálózat három alhálózattal rendelkezik: a "frontend", a "egy midtier" és a "háttér" alhálózatok, amelyek négy telephelyi kapcsolattal rendelkeznek: "DefaultSiteHQ" és három ág.


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

A következő lépések a "DefaultSiteHQ" értéket adja meg a kényszerített bújtatáshoz tartozó alapértelmezett helyként, és konfigurálja a egy midtier és a háttérbeli alhálózatokat a kényszerített bújtatás használatára.

1. Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal. Kapcsolódjon a fiókjához a következő példa használatával:

   ```powershell
   Add-AzureAccount
   ```

1. Hozzon létre egy útválasztási táblázatot. Az útválasztási táblázat létrehozásához használja a következő parancsmagot.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. Adjon hozzá egy alapértelmezett útvonalat az útválasztási táblához. 

   Az alábbi példa egy alapértelmezett útvonalat helyez el az 1. lépésben létrehozott útválasztási táblázathoz. Vegye figyelembe, hogy az egyetlen támogatott útvonal a "0.0.0.0/0" célként megadott előtag a "átjáróban" NextHop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. Rendelje hozzá az útválasztási táblázatot az alhálózatokhoz. 

   Az útválasztási tábla létrehozása és a hozzá tartozó útvonal hozzáadása után a következő példa használatával adja hozzá vagy rendelje az útválasztási táblázatot egy VNet-alhálózathoz. A példa hozzáadja a "MyRouteTable" útválasztási táblázatot a többrétegű VNet-VNet egy midtier és háttérbeli alhálózatához.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. Rendeljen egy alapértelmezett helyet a kényszerített bújtatáshoz. 

   Az előző lépésben a minta-parancsmag parancsfájljai létrehozták az útválasztási táblázatot, és hozzárendelték az útválasztási táblázatot a VNet két alhálózatához. A fennmaradó lépés egy helyi hely kiválasztása a virtuális hálózat többhelyes kapcsolatai között alapértelmezett helyként vagy alagútként.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>További PowerShell-parancsmagok

### <a name="to-delete-a-route-table"></a>Útválasztási táblázat törlése

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Útválasztási táblázat listázása

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Útvonal törlése az útválasztási táblázatból

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Útvonal eltávolítása az alhálózatról

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Alhálózathoz társított útválasztási táblázat listázása

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Alapértelmezett hely eltávolítása VNet VPN-átjáróról

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
