---
title: 'ExpressRoute és S2S VPN egyidejű kapcsolatainak konfigurálása: Azure PowerShell'
description: Konfigurálja a ExpressRoute és egy helyek közötti VPN-kapcsolat konfigurálását, amely a PowerShell használatával a Resource Manager-modellben is létezhet.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/06/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: df88bd9a1d4901b348fbec47ea9e2946542a08e3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440088"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>A ExpressRoute és a helyek közötti egyidejű kapcsolatok konfigurálása a PowerShell használatával
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-howto-coexist-classic.md)
> 
> 

Ez a cikk segítséget nyújt a ExpressRoute és a helyek közötti VPN-kapcsolatok konfigurálásához. A helyek közötti VPN és az ExpressRoute konfigurálásának lehetősége több előnnyel jár. A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként a ExpressRoute, vagy a helyek közötti VPN-kapcsolattal csatlakozhat olyan helyekhez, amelyek nem csatlakoznak a ExpressRoute-on keresztül. A cikkben mindkét forgatókönyv lépéseit ismertetjük. Ez a cikk a Resource Manager-alapú üzemi modell vonatkozik.

Az egyidejű helyek közötti VPN- és ExpressRoute-kapcsolatok konfigurálása több előnnyel jár:

* Konfigurálhat helyek közötti VPN-t biztonságos feladatátvételi útvonalként az ExpressRoute számára. 
* Azt is megteheti, hogy helyek közötti VPN-t használ olyan helyekhez való csatlakozáshoz, amelyek nem az ExpressRoute-on keresztül kapcsolódnak. 

Ebben a cikkben ismertetjük mindkét forgatókönyv konfigurálásának lépéseit. Ez a cikk a Resource Manager-alapú üzemi modellre vonatkozik, és a PowerShellt használja. Ezeket a forgatókönyveket a Azure Portal használatával is konfigurálhatja, bár a dokumentáció még nem érhető el. Először az átjárót is konfigurálhatja. Az új átjáró vagy átjáró-kapcsolatok hozzáadásakor általában nem kell állásidőt fizetnie.

>[!NOTE]
>Ha helyek közötti VPN-t szeretne létrehozni egy ExpressRoute-kapcsolatcsoport között, tekintse meg [ezt a cikket](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Korlátok és korlátozások
* **Kizárólag az útvonalalapú VPN-átjáró támogatott.** Egy Route-alapú [VPN-átjárót](../vpn-gateway/vpn-gateway-about-vpngateways.md)kell használnia. Egy Route-alapú VPN-átjárót is használhat a "házirend-alapú forgalmi választóknak" konfigurált VPN-kapcsolattal, a következő témakörben leírtak szerint: [Csatlakozás több házirend-alapú VPN-eszközhöz](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).
* **Az Azure VPN Gateway ASN-jét 65515-re kell állítani.** Az Azure VPN Gateway támogatja a BGP útválasztási protokollt. Ahhoz, hogy a ExpressRoute és az Azure VPN működjön együtt, a 65515-as alapértelmezett értéken meg kell őriznie az Azure VPN Gateway autonóm rendszer számát. Ha korábban a 65515-es és a 65515-es értéket választotta, a beállítás érvénybe léptetéséhez alaphelyzetbe kell állítania a VPN-átjárót.
* **Az átjáró-alhálózat/27 vagy egy rövidebb előtag**(például/26,/25) lehet, vagy hibaüzenet jelenik meg, amikor hozzáadja a ExpressRoute virtuális hálózati átjárót.
* **A kettős verem vnet nem támogatott az együttélés.** Ha a ExpressRoute IPv6-támogatást és egy kettős verem ExpressRoute-átjárót használ, akkor a VPN Gateway együttes létezése nem lehetséges.

## <a name="configuration-designs"></a>Konfigurációs tervek
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Helyek közötti VPN konfigurálása feladatátvételi útvonalként az ExpressRoute számára
Konfigurálhat egy helyek közötti VPN-kapcsolatot tartalékként az ExpressRoute számára. Ez a kapcsolat csak az Azure privát társviszony-létesítési útvonalhoz társított virtuális hálózatokra vonatkozik. Az Azure Microsoft társviszony-létesítésekhez nem létezik VPN-alapú feladatátvételi megoldás. Minden esetben az ExpressRoute-kapcsolatcsoport az elsődleges kapcsolat. Az adatok csak akkor lesznek a helyek közötti VPN-útvonalon továbbítva, ha az ExpressRoute-kapcsolatcsoport meghibásodik. Az aszimmetrikus útválasztás elkerüléséhez a helyi hálózati konfigurációnak szintén az ExpressRoute-kapcsolatcsoportot kell előnyben részesítenie a helyek közötti VPN helyett. Az ExpressRoute elérési útjának előnyben részesítéséhez beállíthat magasabb helyi prioritást az ExpressRoute által fogadott útvonalakhoz. 

>[!NOTE]
> Ha engedélyezve van a Microsoft ExpressRoute, a ExpressRoute-kapcsolaton keresztül az Azure VPN Gateway nyilvános IP-címét is megkaphatja. Ha a helyek közötti VPN-kapcsolatot biztonsági másolatként szeretné beállítani, konfigurálnia kell a helyszíni hálózatot, hogy a VPN-kapcsolat az internethez legyen irányítva.
>

> [!NOTE]
> Bár a rendszer az ExpressRoute-kapcsolatcsoportot részesíti előnyben a helyek közötti VPN helyett, ha az útvonalak megegyeznek, az Azure a leghosszabb előtag-megfeleltetéssel választja ki a célcsomag útvonalát.
> 
> 

![Diagram, amely a helyek közötti VPN-kapcsolat biztonsági mentését jeleníti meg a ExpressRoute.](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Helyek közötti VPN konfigurálása az ExpressRoute használatával nem csatlakozó helyekhez
A hálózatát konfigurálhatja úgy is, hogy egyes helyek közvetlenül az Azure-hoz kapcsolódnak helyek közötti VPN-en keresztül, míg más helyek az ExpressRoute használatával kapcsolódnak. 

![Egyidejű jelenlét](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Virtuális hálózatot nem konfigurálhat tranzit útválasztóként.
> 
> 

## <a name="selecting-the-steps-to-use"></a>A használni kívánt lépések kiválasztása
Két különböző eljáráscsoport közül választhat. A konfigurálás választott módja attól függ, hogy rendelkezik-e meglévő virtuális hálózattal, amelyhez csatlakozni szeretne, vagy egy új virtuális hálózatot szeretne létrehozni.

* Nem rendelkezem VNettel, és létre kell hoznom egyet.
  
    Ha még nem rendelkezik virtuális hálózattal, ez az eljárás lépésről lépésre végigvezeti az új virtuális hálózat létrehozásának folyamatán a Resource Manager-alapú üzemi modellnek megfelelően, valamint az új ExpressRoute- és helyek közötti VPN-kapcsolatok létrehozásának folyamatán. Virtuális hálózat konfigurálásához kövesse az [Új virtuális hálózat és egyidejű kapcsolatok létrehozása](#new) szakaszban foglalt lépéseket.
* Már rendelkezem egy Resource Manager-alapú üzemi modell szerinti VNettel.
  
    Előfordulhat, hogy már rendelkezik üzemelő virtuális hálózattal egy létező helyek közötti VPN- vagy ExpressRoute-kapcsolattal. Ebben a forgatókönyvben, ha az átjáró alhálózati maszkja /28 vagy kisebb (/28, /29 stb.), akkor törölnie kell a meglévő átjárót. Az [Egyidejű kapcsolatok konfigurálása meglévő VNet számára](#add) szakasz végigvezeti az átjáró törlésének, majd az új ExpressRoute- és helyek között VPN-kapcsolatok létrehozásának folyamatán.
  
    Ha törli és újra létrehozza az átjárót, akkor a létesítmények közötti kapcsolatoknál állásidő fog jelentkezni. Azonban, ha megfelelően vannak konfigurálva, a virtuális gépek és a szolgáltatások továbbra is képesek lesznek kommunikálni a terheléselosztón keresztül az átjáró konfigurálása közben.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Új virtuális hálózat és egyidejű kapcsolatok létrehozása
Az eljárás a VNetek, valamint az egyidejűleg jelenlévő helyek közötti és ExpressRoute-kapcsolatok létrehozásának módját ismerteti. Az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja.

1. Jelentkezzen be, és válassza ki az előfizetését.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Változók beállítása

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Hozzon létre egy virtuális hálózatot az átjáró-alhálózattal együtt. A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd a [virtuális hálózatok létrehozásával](../virtual-network/manage-virtual-network.md#create-a-virtual-network) foglalkozó témakört. Az alhálózatok létrehozásával kapcsolatos további információkért lásd az [alhálózatok létrehozásával](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) foglalkozó témakört.
   
   > [!IMPORTANT]
   > Az átjáró-alhálózat /27 vagy egy rövidebb előtag kell legyen (például /26 vagy /25).
   > 
   > 
   
    Hozzon létre egy új VNetet.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Adjon hozzá alhálózatokat.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Mentse a VNet konfigurációját.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Ezután hozza létre a helyek közötti VPN-átjárót. A VPN-átjáró konfigurálásával kapcsolatos további információkért lásd: [VNet létrehozása helyek közötti kapcsolattal](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). A GatewaySku csak *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* és *Nagy teljesítményű* VPN-átjárók esetén támogatott. Az alapszintű termékváltozat nem támogatja az ExpressRoute-VPN-átjárók egyszerre konfigurált beállításait. A VpnType paramétert *RouteBased* értékre kell állítania.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Az Azure-os VPN-átjáró támogatja a BGP útválasztási protokollt. A virtuális hálózat ASN (AS-szám) értékét úgy határozhatja meg, hogy az alábbi parancshoz hozzáadja az -Asn kapcsolót. Ha ezt a paramétert nem határozza meg, az alapértelmezés szerinti AS-szám a 65515 lesz.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    A következőkben megtalálhatja a BGP-társviszony-létesítés IP-címét és AS-számát, amelyeket az Azure használ a VPN-átjáróhoz: $azureVpn.BgpSettings.BgpPeeringAddress és $azureVpn.BgpSettings.Asn. További információk: [A BGP konfigurálása](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) az Azure-alapú VPN-átjáróhoz.
5. Hozzon létre egy helyi VPN-átjáró entitást. Ez a parancs nem konfigurálja a helyszíni VPN-átjárót. Ehelyett a helyi átjáró beállításai, például a nyilvános IP-cím és a helyszíni címtér megadására szolgál, hogy az Azure VPN-átjáró kapcsolódhasson hozzá.
   
    Ha a helyi VPN-eszköz csak a statikus útválasztást támogatja, a következő módon konfigurálhatja a statikus útvonalakat:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Ha a helyi VPN-eszköz támogatja a BGP-t, és Ön szeretné engedélyezni a dinamikus útválasztást, ismernie kell a helyi VPN-eszköz által használt BGP-társviszony-létesítés IP-címét és AS-számát.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Konfigurálja helyi VPN-eszközét, hogy az új Azure VPN-átjáróhoz csatlakozzon. A VPN-eszköz konfigurálásával kapcsolatos további információkért lásd: [VPN-eszköz konfigurálása](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Csatlakoztassa az Azure-on a helyek közötti VPN-átjárót a helyi átjáróhoz.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Ha egy meglévő ExpressRoute-kapcsolatcsoporthoz csatlakozik, hagyja ki a 8. és 9. lépést, és ugorjon a 10.-re. ExpressRoute-kapcsolatcsoportok konfigurálása. ExpressRoute-kapcsolatcsoportok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md).


9. Azure-beli privát társviszony konfigurálása az ExpressRoute-kapcsolatcsoporton keresztül. Azure-beli privát társviszony ExpressRoute-kapcsolatcsoporton keresztüli konfigurálásáról további információért lásd: [társviszony létesítésének konfigurálása](expressroute-howto-routing-arm.md)

10. <a name="gw"></a>Hozzon létre egy ExpressRoute-átjárót. Az ExpressRoute-átjáró konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute-átjáró konfigurálása](expressroute-howto-add-gateway-resource-manager.md). A GatewaySKU paraméterben a *Standard*, *HighPerformance* vagy *UltraPerformance* értéket kell megadni.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Csatlakoztassa az ExpressRoute-átjárót az ExpressRoute-kapcsolatcsoporthoz. A lépés végrehajtásával létrejön a kapcsolat a helyszíni hálózat és az Azure között az ExpressRoute-on keresztül. A csatolási művelettel kapcsolatos további információkért lásd: [VNetek csatolása az ExpressRoute-hoz](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Egyidejű kapcsolatok konfigurálása meglévő VNet számára
Ha a virtuális hálózat egyetlen virtuális hálózati átjáróval (például helyek közötti VPN-átjáróval) rendelkezik, és egy másik, eltérő típusú átjárót (például egy ExpressRoute-átjárót) szeretne hozzáadni, ellenőrizze az átjáró-alhálózat méretét. Ha az átjáró-alhálózat /27 vagy nagyobb, kihagyhatja az alábbi lépéseket, és az előző szakaszban ismertetett lépéseket követve hozzáadhat egy helyek közötti VPN-átjárót vagy egy ExpressRoute-átjárót. Ha az átjáró-alhálózat /28 vagy /29, először törölnie kell a virtuális hálózati átjárót, és növelnie kell az átjáró-alhálózat méretét. A jelen szakaszban ismertetett lépések bemutatják, mindez hogyan valósítható meg.

Az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja.

1. Törölje a meglévő ExpressRoute- vagy helyek közötti VPN-átjárót.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Törölje az átjáró-alhálózatot.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Adjon hozzá egy átjáró-alhálózatot, amely legfeljebb /27.
   
   > [!NOTE]
   > Ha már nincs elegendő IP-cím a virtuális hálózaton az átjáró-alhálózat méretének növeléséhez, további IP-címtereket kell hozzáadnia.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Mentse a VNet konfigurációját.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. Ezen a ponton egy átjáró nélküli virtuális hálózattal rendelkezik. Új átjárók létrehozásához és a kapcsolatok beállításához használja az alábbi példákat:

   Állítsa be a változókat.

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   Hozzon létre egy átjárót.

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   Hozza létre a kapcsolatot.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Pont-hely konfiguráció hozzáadása a VPN-átjáróhoz

Az alábbi lépések végrehajtásával pont – hely konfigurációt adhat hozzá a VPN-átjáróhoz egy párhuzamos telepítésben. A VPN-főtanúsítvány feltöltéséhez a PowerShellt helyileg kell telepítenie a számítógépre, vagy a Azure Portalt kell használnia.

1. Adja hozzá a VPN-ügyfél címterét.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Töltse fel a VPN-főtanúsítványt az Azure-ba a VPN-átjáró számára. Ebben a példában feltételezzük, hogy a főtanúsítványt a helyi gépen tárolja a rendszer, ahol a következő PowerShell-parancsmagok futnak, és a PowerShell helyileg fut. A tanúsítványt a Azure Portal használatával is feltöltheti.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

## <a name="to-enable-transit-routing-between-expressroute-and-azure-vpn"></a>Az átviteli útválasztás engedélyezése a ExpressRoute és az Azure VPN között
Ha engedélyezni szeretné a csatlakozást a ExpressRoute-hez csatlakozó helyi hálózat vagy egy, a helyek közötti VPN-kapcsolathoz csatlakozó helyi hálózat között, be kell állítania az [Azure Route Servert](../route-server/expressroute-vpn-support.md).

A pont-hely VPN-ekkel kapcsolatos további információkért lásd: [Pont-hely kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Következő lépések
A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
