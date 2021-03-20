---
title: 'Azure VPN Gateway: átjárók csatlakoztatása több helyszíni házirend-alapú VPN-eszközhöz'
description: Konfiguráljon egy Azure Route-alapú VPN-átjárót több házirend alapú VPN-eszközre a Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2a85204fef026940394a19934bef1c631a8e2d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89418878"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Azure VPN-átjárók csatlakoztatása több helyszíni, a PowerShell-t használó helyi házirend-alapú VPN-eszközhöz

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Azure Route-alapú VPN-átjárót több helyszíni, házirend-alapú VPN-eszközhöz való csatlakozáshoz, amely Egyéni IPsec/IKE-házirendeket használ a S2S VPN-kapcsolatokon.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Házirend-alapú és Route-alapú VPN-átjárók

A házirend-alapú és *az útválasztási alapú* VPN-eszközök különböznek az IPSec-forgalom választóinak a kapcsolaton való beállításának módjában:

* **Házirend-alapú** A VPN-eszközök mindkét hálózat előtagjainak kombinációit használják annak meghatározására, hogy a forgalom titkosítása/visszafejtése hogyan történjen az IPsec-alagutakon keresztül. Ez általában a csomagszűrés által elvégezhető tűzfalakon épül fel. Az IPsec-alagút titkosítási és visszafejtési műveletei hozzáadódnak a csomagok szűrését és feldolgozását végző motorhoz.
* **Route-alapú** A VPN-eszközök bármilyen típusú (helyettesítő) forgalmi választót használnak, és lehetővé teszik a különböző IPsec-alagutakra irányuló útválasztási/továbbítási táblák közvetlen forgalmát. Ez általában olyan útválasztó platformokra épül, ahol az egyes IPsec-alagutak hálózati adapterként vagy VTI (virtuális bújtatási felületen) vannak modellezve.

A következő diagramok kiemelik a két modellt:

### <a name="policy-based-vpn-example"></a>Házirend-alapú VPN-példa
![házirend-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Route-alapú VPN-példa
![Route-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-támogatás házirend alapú VPN-hez
Jelenleg az Azure a VPN-átjárók mindkét üzemmódját támogatja: Route-alapú VPN-átjárók és házirend-alapú VPN-átjárók. Különböző belső platformokra épülnek, amelyek különböző specifikációkat eredményeznek:

| Kategória | Házirendalapú VPN Gateway | Útvonalalapú VPN Gateway | Útvonalalapú VPN Gateway |
| -------- | ----------------------- | ---------------------- | ---------------------- |---                                                 |
| **Azure Gateway SKU**    | Alapszintű                       | Alapszintű                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **IKE-verzió**          | IKEv1                       | IKEv2                            | IKEv1 és IKEv2                         |
| **S2S-kapcsolatok maximális száma** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Az Egyéni IPsec/IKE-szabályzattal mostantól konfigurálhatja az Azure Route-alapú VPN-átjárókat, hogy a "**PolicyBasedTrafficSelectors**" beállítással rendelkező előtag-alapú forgalmi választókat használják a helyi házirend-alapú VPN-eszközökhöz való csatlakozáshoz. Ez a funkció lehetővé teszi, hogy az Azure-beli virtuális hálózatról és a VPN-átjáróról több helyszíni házirend-alapú VPN-vagy tűzfal-eszközhöz kapcsolódjon, így a jelenlegi Azure Policy-alapú VPN-átjárók egyetlen csatlakozási korlátját távolítja el.

> [!IMPORTANT]
> 1. A kapcsolat engedélyezéséhez a helyszíni házirend-alapú VPN-eszközöknek támogatniuk kell a **IKEv2** az Azure Route-alapú VPN-átjáróhoz való csatlakozáshoz. Keresse meg a VPN-eszköz specifikációit.
> 2. A házirend-alapú VPN-eszközökön keresztül az ezzel a mechanizmussal csatlakozó helyszíni hálózatok csak az Azure-beli virtuális hálózathoz csatlakozhatnak; **nem tudnak átjutni más helyszíni hálózatokra vagy virtuális hálózatokra ugyanazon Azure VPN-átjárón keresztül**.
> 3. A konfigurációs beállítás része az Egyéni IPsec/IKE-kapcsolati házirendnek. Ha engedélyezi a házirend alapú forgalom-választó beállítást, meg kell adnia a teljes szabályzatot (IPsec/IKE titkosítási és integritási algoritmusok, kulcs erőssége és SA-élettartama).

Az alábbi ábrán látható, hogy az Azure VPN Gateway-en keresztüli tranzit-útválasztás miért nem működik a házirend-alapú kapcsolóval:

![házirend alapú tranzit](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Ahogy az ábrán is látható, az Azure VPN Gateway a virtuális hálózatról származó forgalmi választókat is tartalmaz a helyszíni hálózati előtagok számára, a kapcsolat nélküli előtagokat azonban nem. Például a helyszíni 2., a 3. és a 4. hely egyaránt képes kommunikálni a VNet1, de az Azure VPN-átjárón keresztül nem tud csatlakozni egymáshoz. Az ábrán az Azure VPN Gateway ezen konfiguráció alatt nem elérhető, egymással összekapcsolt forgalom-választói láthatók.

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

A cikkben szereplő utasítások ugyanezt a példát követik, mint az [IPSec/IKE-szabályzat konfigurálása S2S vagy VNet-to-VNet kapcsolatok](vpn-gateway-ipsecikepolicy-rm-powershell.md) számára a S2S VPN-kapcsolat létesítéséhez. Ez a következő ábrán látható:

![S2S – szabályzat](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

A kapcsolódást engedélyező munkafolyamat:
1. Hozza létre a virtuális hálózatot, a VPN-átjárót és a helyi hálózati átjárót a létesítmények közötti kapcsolathoz.
2. Hozzon létre egy IPsec/IKE-házirendet.
3. Alkalmazza a házirendet a S2S vagy a VNet-VNet közötti kapcsolatok létrehozásakor, és **engedélyezze a házirend-alapú forgalom-választókat** a kapcsolatban.
4. Ha a kapcsolódás már létrejött, a szabályzatot alkalmazhatja vagy frissítheti egy meglévő kapcsolatban.

## <a name="before-you-begin"></a>Előkészületek

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Házirend-alapú forgalmi választók engedélyezése

Ebből a szakaszból megtudhatja, hogyan engedélyezheti a házirend-alapú forgalmi választókat a kapcsolatok esetében. Győződjön meg arról, hogy végrehajtotta [az IPSec/IKE-házirend konfigurálása című cikk 3. részét](vpn-gateway-ipsecikepolicy-rm-powershell.md). A cikkben szereplő lépések ugyanazt a paramétereket használják.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Kapcsolódjon az előfizetéshez, és állapítsa meg a változókat

1. Ha a PowerShellt helyileg futtatja a számítógépen, jelentkezzen be a *AzAccount* parancsmag használatával. Ehelyett használja a böngészőben a Azure Cloud Shell.

2. Deklarálja a változókat. Ebben a gyakorlatban a következő változókat használjuk:

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
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
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

1. Hozzon létre egy erőforráscsoportot.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. A következő példa segítségével hozza létre a virtuális hálózati TestVNet1 három alhálózattal és a VPN-átjáróval. Ha szeretné helyettesíteni az értékeket, fontos, hogy mindig az átjáró-alhálózatot nevezze el, amely kifejezetten "GatewaySubnet". Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>2. lépés – S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

1. Hozzon létre egy IPsec/IKE-házirendet.

   > [!IMPORTANT]
   > Létre kell hoznia egy IPsec/IKE-szabályzatot, hogy engedélyezze az "UsePolicyBasedTrafficSelectors" lehetőséget a kapcsolódáson.

   Az alábbi példa egy IPsec/IKE-házirendet hoz létre az alábbi algoritmusokkal és paraméterekkel:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS nincs, SA élettartama 14400 másodperc & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Hozza létre a S2S VPN-kapcsolat házirend-alapú forgalmi választókkal és IPsec/IKE-szabályzattal, és alkalmazza az előző lépésben létrehozott IPsec/IKE-házirendet. Vegye figyelembe a további "-UsePolicyBasedTrafficSelectors $True" paramétert, amely lehetővé teszi a házirend-alapú forgalom-választókat a kapcsolaton.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. A lépések elvégzése után a S2S VPN-kapcsolat az IPsec/IKE-házirendet fogja használni, és engedélyezi a házirend-alapú forgalom-választókat a kapcsolaton. Ugyanezeket a lépéseket megismételve további kapcsolatokat adhat hozzá a helyi házirend-alapú VPN-eszközökhöz ugyanahhoz az Azure VPN-átjáróhoz.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Házirend-alapú forgalom-választók frissítése
Ebből a szakaszból megtudhatja, hogyan frissítheti a házirend-alapú forgalmi választókat egy meglévő S2S VPN-kapcsolathoz.

1. A kapcsolatok erőforrásának beolvasása.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Tekintse meg a házirend-alapú forgalom választói beállítást.
A következő sor azt mutatja be, hogy a házirend alapú forgalom-választókat használja-e a rendszer a kapcsolatban:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Ha a sor "true" (**igaz**) értéket ad vissza, akkor a házirend-alapú forgalom választói vannak konfigurálva a kapcsolatban. Ellenkező esetben a "**false**" értéket adja vissza.
1. A kapcsolatok erőforrásának beszerzése után engedélyezheti vagy letilthatja a házirend-alapú forgalmi választókat egy adott kapcsolatban.

   - Az engedélyezéshez

      A következő példa engedélyezi a házirend alapú forgalom-választókat, de változatlanul hagyja az IPsec/IKE-házirendet:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - A letiltáshoz

      A következő példa letiltja a házirend alapú forgalom-választókat, de változatlanul hagyja az IPsec/IKE-házirendet:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).

Tekintse át az [IPSec/IKE-házirend konfigurálása a S2S VPN-hez vagy a VNet-VNet kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md) című témakört az egyéni IPSec/IKE-házirendekkel kapcsolatos további részletekért.
