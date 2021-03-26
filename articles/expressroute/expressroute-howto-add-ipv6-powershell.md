---
title: 'Azure ExpressRoute: IPv6-támogatás hozzáadása a Azure PowerShell használatával'
description: Ismerje meg, hogyan adhat hozzá IPv6-támogatást az Azure-környezetekhez való kapcsolódáshoz Azure PowerShell használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 7a9ac98a9566986767016720fda245712197b27f
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566540"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>IPv6-támogatás hozzáadása a Azure PowerShell (előzetes verzió) használatával a privát partnerek számára

Ez a cikk azt ismerteti, hogyan adhat hozzá IPv6-támogatást a ExpressRoute-on keresztül az Azure-beli erőforrásokhoz való kapcsolódáshoz Azure PowerShell használatával.

> [!Note]
> Ez a funkció jelenleg előzetes verzióként érhető el az [Azure-régiókban Availability Zones](../availability-zones/az-region.md#azure-regions-with-availability-zones). Az ExpressRoute-áramkör ezért bármely egymással létrehozott hely használatával hozható létre, de a csatlakozáshoz használt IPv6-alapú központi telepítéseknek Availability Zones-vel rendelkező régiókban kell lenniük.

## <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Regisztráljon a nyilvános előzetes verzióra
Az IPv6-támogatás hozzáadása előtt regisztrálnia kell az előfizetését. A regisztráláshoz tegye a következőket Azure PowerShellon keresztül:
1.  Jelentkezzen be az Azure-ba, és válassza ki az előfizetést. Ezt a ExpressRoute áramkört tartalmazó előfizetéshez, valamint az Azure-beli üzemelő példányokat tartalmazó előfizetéshez kell elvégeznie (ha azok eltérnek).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. A nyilvános előzetes verzióra vonatkozó előfizetés regisztrálására vonatkozó kérelem a következő paranccsal:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

A kérést a ExpressRoute csapata 2-3 munkanapon belül jóváhagyja.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>IPv6-alapú privát társak hozzáadása a ExpressRoute-áramkörhöz

1. [Hozzon létre egy ExpressRoute áramkört](./expressroute-howto-circuit-arm.md) , vagy használjon egy meglévő áramkört. Az áramkör beolvasása a **Get-AzExpressRouteCircuit** parancs futtatásával:

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. A **Get-AzExpressRouteCircuitPeeringConfig** futtatásával kérje le az áramkör privát társítási konfigurációját:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Adjon hozzá egy IPv6-alapú privát társat a meglévő IPv4-alapú privát kapcsolati konfigurációhoz. Adjon meg egy pár/126 IPv6-alhálózatot, amelyet Ön az elsődleges és másodlagos hivatkozások számára is tartalmaz. Az egyes alhálózatokból az első használható IP-címet az útválasztóhoz rendeli hozzá, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.

    > [!Note]
    > A társ ASN-nek és a VlanId meg kell egyezniük az IPv4-alapú privát társ-konfigurációban találhatókkal.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. A konfiguráció sikeres mentése után a **Get-AzExpressRouteCircuit** parancs futtatásával kérje le újra az áramkört. A válasznak az alábbi példához hasonlóan kell kinéznie:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Meglévő virtuális hálózathoz való kapcsolódás frissítése

Kövesse az alábbi lépéseket, ha az Azure-erőforrások meglévő környezete egy olyan régióban található, amelyhez Availability Zones szeretné használni az IPv6-alapú privát kapcsolatot.

1. Kérje le azt a virtuális hálózatot, amelyhez a ExpressRoute áramkör csatlakozik.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Adjon hozzá egy IPv6-címtartományt a virtuális hálózathoz.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Adja hozzá az IPv6-címtartományt az átjáró-alhálózathoz. Az átjáró IPv6-alhálózatának/64 vagy nagyobbnak kell lennie.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Ha van egy már meglévő zóna – redundáns átjáró, futtassa az alábbi parancsot az IPv6-kapcsolat engedélyezéséhez. Ellenkező esetben [hozza létre a virtuális hálózati átjárót](./expressroute-howto-add-gateway-resource-manager.md) egy felesleges SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) használatával.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Új virtuális hálózathoz való kapcsolódás létrehozása

Kövesse az alábbi lépéseket, ha azt tervezi, hogy új Azure-erőforrás-készlethez szeretne csatlakozni egy olyan régióban, ahol az IPv6-alapú privát társas kapcsolaton keresztül Availability Zones.

1. Hozzon létre egy kettős veremből álló virtuális hálózatot IPv4-és IPv6-címtartomány mellett. További információt a [virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md#create-a-virtual-network)című témakörben talál.

2. [Hozza létre a kettős verem átjárójának alhálózatát](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. [Hozza létre a virtuális hálózati átjárót](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) egy zóna – redundáns SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) használatával. Ha azt tervezi, hogy FastPath használ, használja a ErGw3AZ (vegye figyelembe, hogy ez csak a ExpressRoute Direct-t használó áramkörök esetén érhető el).

4. [Csatolja a virtuális hálózatot a ExpressRoute-áramkörhöz](./expressroute-howto-linkvnet-arm.md).

## <a name="limitations"></a>Korlátozások
Míg az IPv6-támogatás elérhető az Availability Zones-vel rendelkező régiókban üzemelő példányokhoz való kapcsolódáshoz, nem támogatja a következő használati eseteket:

* Az Azure-beli üzembe helyezések kapcsolatai nem AZ ExpressRoute Gateway SKU használatával
* Kapcsolatok a nem az-régiókban üzemelő példányokhoz
* ExpressRoute-áramkörök közötti Global Reach kapcsolatok
* ExpressRoute használata virtuális WAN-kapcsolattal
* FastPath nem ExpressRoute közvetlen áramkörökkel
* FastPath a következő egyenrangú helyszíneken található áramkörökkel: Dubaj
* Együttélés VPN Gateway

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos problémák elhárításához tekintse meg a következő cikkeket:

* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)