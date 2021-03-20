---
title: Partneri VPN-eszközök konfigurációja az Azure VPN Gateway átjáróhoz való csatlakozáshoz
description: Ez a cikk áttekintést nyújt a partner VPN-eszköz konfigurációkról az Azure VPN Gateway-hez való csatlakozáshoz.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 484dad65310efaa60e8744b2f122b5e44ae13565
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880152"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>A partneri VPN-eszközök konfigurációinak áttekintése
Ez a cikk áttekintést nyújt a helyszíni VPN-eszközök konfigurálásáról az Azure VPN Gateway-hez való csatlakozáshoz. A minta Azure-beli virtuális hálózat és a VPN-átjáró beállításával megtudhatja, hogyan csatlakozhat a különböző helyszíni VPN-eszközök konfigurációhoz ugyanazzal a paraméterekkel.



## <a name="device-requirements"></a>Eszközre vonatkozó követelmények
Az Azure VPN Gateway standard IPsec/IKE Protocol Suite-csomagokat használ a helyek közötti (S2S) VPN-alagutakhoz. Az IPsec/IKE-paraméterek és az Azure VPN Gateway titkosítási algoritmusok listájáért lásd: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md). Az adott kapcsolatok pontos algoritmusait és erősségeit is megadhatja a [titkosítási követelmények](vpn-gateway-about-compliance-crypto.md)című témakörben leírtak szerint.

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Egyetlen VPN-alagút
A mintában az első konfiguráció egyetlen S2S VPN-alagutat tartalmaz az Azure VPN Gateway és egy helyszíni VPN-eszköz között. Igény szerint konfigurálhatja a [Border Gateway Protocol (BGP) a VPN-alagúton keresztül](#bgp).

![Egyetlen S2S VPN-alagút diagramja](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Az egyetlen VPN-alagút beállításával kapcsolatos részletes utasításokért lásd: [helyek közötti kapcsolat konfigurálása](./tutorial-site-to-site-portal.md). A következő szakaszokban adja meg a minta konfigurációjának kapcsolódási paramétereit, és adjon meg egy PowerShell-parancsfájlt, amely segítséget nyújt az első lépésekhez.

### <a name="connection-parameters"></a>Kapcsolódási paraméterek
Ez a szakasz az előző szakaszokban leírt példák paramétereit sorolja fel.

| **Paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózati címek előtagjai        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN Gateway IP-címe         | Azure VPN Gateway IP         |
| Helyszíni címek előtagjai | 10.51.0.0/16<br>10.52.0.0/16 |
| Helyszíni VPN-eszköz IP-címe    | Helyszíni VPN-eszköz IP-címe    |
| * Virtual Network BGP ASN-es                | 65010                        |
| * Azure BGP-társ IP-címe           | 10.12.255.30                 |
| * Helyszíni BGP ASN-es         | 65050                        |
| * Helyszíni BGP-társ IP-címe     | 10.52.255.254                |

\* Nem kötelező paraméter a BGP-hez.

### <a name="sample-powershell-script"></a>PowerShell-parancsfájl mintája
Ez a szakasz egy minta parancsfájlt tartalmaz a kezdéshez. Részletes útmutatásért lásd: [S2S VPN-kapcsolat létrehozása a PowerShell használatával](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>Választható Egyéni IPsec/IKE-szabályzat használata a UsePolicyBasedTrafficSelectors
Ha a VPN-eszközök nem támogatják a bármilyen forgalmi választók, például az útvonal-vagy VTI konfigurációkat, hozzon létre egy egyéni IPsec/IKE-házirendet a [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) kapcsolóval.

> [!IMPORTANT]
> Létre kell hoznia egy IPsec/IKE-szabályzatot, amely engedélyezi a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolatban.


A minta parancsfájl egy IPsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA élettartam 7 200 másodperc és 20 480 000 KB (20 GB)

A szkript alkalmazza az IPsec/IKE-házirendet, és engedélyezi a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolaton.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>Választható A BGP használata a S2S VPN-kapcsolaton
A S2S VPN-kapcsolat létrehozásakor igény szerint [a BGP-t is használhatja a VPN-átjáróhoz](vpn-gateway-bgp-resource-manager-ps.md). Ez a megközelítés két különbséggel rendelkezik:

* A helyszíni címek előtagjai lehetnek egyetlen gazdagép-címek. A helyszíni BGP-társ IP-címe a következőképpen van megadva:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* A kapcsolódás létrehozásakor a **-EnableBGP** kapcsolót kell megadnia $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Következő lépések
Az aktív-aktív VPN-átjárók beállításával kapcsolatos részletes utasításokért lásd: [aktív-aktív VPN-átjárók konfigurálása a létesítmények közötti és VNet-VNet kapcsolatokhoz](vpn-gateway-activeactive-rm-powershell.md).