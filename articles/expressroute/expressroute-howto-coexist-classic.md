---
title: 'ExpressRoute és S2S VPN egyidejű kapcsolatainak konfigurálása: klasszikus'
description: A cikk bemutatja az ExpressRoute- és egy helyek közötti VPN-kapcsolat konfigurálását, amelyek párhuzamosan használhatók a klasszikus üzembehelyezési modellben.
documentationcenter: na
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: 70407d726b697dd6c4dfb6777cf6c445092d3b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206325"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Párhuzamos ExpressRoute- és párhuzamos helyek közötti kapcsolatok konfigurálása (klasszikus)
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-howto-coexist-classic.md)
> 
> 

Ez a cikk segítséget nyújt a ExpressRoute és a helyek közötti VPN-kapcsolatok konfigurálásához. A helyek közötti VPN és az ExpressRoute konfigurálásának lehetősége több előnnyel jár. A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként a ExpressRoute, vagy a helyek közötti VPN-kapcsolattal csatlakozhat olyan helyekhez, amelyek nem csatlakoznak a ExpressRoute-on keresztül. A cikkben mindkét forgatókönyv lépéseit ismertetjük. Ez a cikk a klasszikus üzembehelyezési modellre vonatkozik. Ez a konfiguráció a portálon nem érhető el.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportokat előre konfigurálni kell, mielőtt végrehajtaná az alábbi utasításokat. Mielőtt folytatná az alábbi lépésekkel, az útmutatásoknak megfelelően [hozzon létre egy ExpressRoute-kapcsolatcsoportot](expressroute-howto-circuit-classic.md) és [konfigurálja az útválasztást](expressroute-howto-routing-classic.md).
> 
> 

## <a name="limits-and-limitations"></a>Korlátok és korlátozások
* **A tranzit útválasztás nem támogatott.** Nem hajthat végre útválasztást (az Azure-on keresztül) a helyek közötti VPN használatával csatlakoztatott helyi hálózat és az ExpressRoute használatával csatlakoztatott helyi hálózat között.
* **A pont–hely kapcsolat nem támogatott.** Pont–hely típusú VPN-kapcsolatok nem engedélyezhetők az ExpressRoute-hoz csatlakozó VNet felé. A Pont–hely VPN és az ExpressRoute nem létezhet ugyanazon VNeten belül.
* **A kényszerített bújtatás nem engedélyezhető a helyek közötti VPN-átjárón.** Az internetes forgalmat csak a helyszíni hálózatra „kényszerítheti” az ExpressRoute-on keresztül.
* **Az alapszintű termékváltozat átjárója nem támogatott.** Nem Basic SKU-átjárót kell használnia [ExpressRoute-](expressroute-about-virtual-network-gateways.md) és [VPN-átjáróként](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Kizárólag az útvonalalapú VPN-átjáró támogatott.** Útvonalalapú [VPN-átjárót](../vpn-gateway/vpn-gateway-about-vpngateways.md) kell használnia.
* **A VPN-átjáróhoz statikus útvonalat kell konfigurálni.** Ha a helyi hálózat az ExpressRoute-hoz és a helyek közötti VPN-hez is csatlakozik, konfigurálnia kell egy statikus útvonalat a helyi hálózaton a helyek közötti VPN-kapcsolat a nyilvános internetre történő átirányításához.
* **Elsőként az ExpressRoute-átjárót kell konfigurálnia.** Először az ExpressRoute-átjárót kell létrehoznia, mielőtt felvenné a helyek közötti VPN-átjárót.

## <a name="configuration-designs"></a>Konfigurációs tervek
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Helyek közötti VPN konfigurálása feladatátvételi útvonalként az ExpressRoute számára
Konfigurálhat egy helyek közötti VPN-kapcsolatot tartalékként az ExpressRoute számára. Ez csak az Azure privát társviszony-létesítési útvonalhoz társított virtuális hálózatokra vonatkozik. Az Azure nyilvános és a Microsoft társviszony-létesítésekhez nem létezik VPN-alapú feladatátvételi megoldás. Minden esetben az ExpressRoute-kapcsolatcsoport az elsődleges kapcsolat. Az adatok csak akkor lesznek a helyek közötti VPN-útvonalon továbbítva, ha az ExpressRoute-kapcsolatcsoport meghibásodik. 

> [!NOTE]
> Bár a rendszer az ExpressRoute-kapcsolatcsoportot részesíti előnyben a helyek közötti VPN helyett, ha az útvonalak megegyeznek, az Azure a leghosszabb előtag-megfeleltetéssel választja ki a célcsomag útvonalát.
> 
> 

![Diagram, amely a helyek közötti VPN-kapcsolat biztonsági mentését jeleníti meg a ExpressRoute.](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Helyek közötti VPN konfigurálása az ExpressRoute használatával nem csatlakozó helyekhez
A hálózatát konfigurálhatja úgy is, hogy egyes helyek közvetlenül az Azure-hoz kapcsolódnak helyek közötti VPN-en keresztül, míg más helyek az ExpressRoute használatával kapcsolódnak. 

![Egyidejű jelenlét](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Virtuális hálózatot nem konfigurálhat tranzit útválasztóként.
> 
> 

## <a name="selecting-the-steps-to-use"></a>A használni kívánt lépések kiválasztása
Két különböző eljáráscsoport közül választhat az egyidejűleg használható kapcsolatok konfigurálásához. A konfigurálás választott módja attól függ, hogy rendelkezik-e meglévő virtuális hálózattal, amelyhez csatlakozni szeretne, vagy egy új virtuális hálózatot szeretne létrehozni.

* Nem rendelkezem VNettel, és létre kell hoznom egyet.
  
    Ha még nem rendelkezik virtuális hálózattal, ez az eljárás lépésről lépésre végigvezeti az új virtuális hálózat létrehozásának folyamatán a klasszikus üzembehelyezési modellnek megfelelően, valamint az új ExpressRoute- és helyek közötti VPN-kapcsolatok létrehozásának folyamatán. A konfiguráláshoz kövesse a cikk az [Új virtuális hálózat és egyidejű kapcsolatok létrehozása](#new) szakaszában foglalt lépéseket.
* Már rendelkezem egy klasszikus üzembehelyezési modell szerinti VNettel.
  
    Előfordulhat, hogy már rendelkezik üzemelő virtuális hálózattal egy létező helyek közötti VPN- vagy ExpressRoute-kapcsolattal. A [meglévő VNet egyidejű kapcsolatainak konfigurálására szolgáló](#add) cikk szakasz végigvezeti az átjáró törlésének, majd az új ExpressRoute és helyek közötti VPN-kapcsolatok létrehozásán. Ügyeljen arra, hogy az új kapcsolatok létrehozásakor a lépéseket szigorú sorrendben kell végrehajtani. Az átjárók és kapcsolatok létrehozására ne használja más cikkek utasításait.
  
    Ebben az eljárásban az egyidejű kapcsolatok létrehozásához törölnie kell az átjárót, majd új átjárókat kell konfigurálnia. Ez azt jelenti, hogy a helyszínek közötti kapcsolatok esetében állásidővel kell számolnia, amíg törli és újra létrehozza az átjárót és a kapcsolatokat, azonban a virtuális gépeket és a szolgáltatásokat nem kell áttelepítenie egy új virtuális hálózatra. Ha megfelelően vannak konfigurálva, a virtuális gépek és a szolgáltatások továbbra is képesek lesznek kommunikálni a terheléselosztón keresztül az átjáró konfigurálása közben.

## <a name="install-powershell-cmdlets"></a>PowerShell-parancsmagok telepítése

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Új virtuális hálózat és egyidejű kapcsolatok létrehozása
Az eljárás a VNetek, valamint az egyidejűleg jelenlévő helyek közötti és ExpressRoute-kapcsolatok létrehozásának módját ismerteti.

1. Az Azure PowerShell-parancsmagok legújabb verzióit kell telepítenie. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/). Vegye figyelembe, hogy az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja. 
2. Hozzon létre egy sémát a virtuális hálózat számára. Az új konfigurációs sémával kapcsolatos információkért lásd: [Azure Virtual Network konfigurációs séma](/previous-versions/azure/reference/jj157100(v=azure.100)).
   
    Amikor létrehozza a sémát, mindenképp a következő értékeket használja:
   
   * A virtuális hálózat átjáró-alhálózata /27 vagy egy rövidebb előtag kell legyen (például /26 vagy /25).
   * Az átjáró kapcsolattípusa: „Dedikált”.
     
    ```xml
    <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
      <AddressSpace>
        <AddressPrefix>10.17.159.192/26</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="Subnet-1">
          <AddressPrefix>10.17.159.192/27</AddressPrefix>
        </Subnet>
        <Subnet name="GatewaySubnet">
          <AddressPrefix>10.17.159.224/27</AddressPrefix>
          /Subnet>
      </Subnets>
      <Gateway>
        <ConnectionsToLocalNetwork>
          <LocalNetworkSiteRef name="MyLocalNetwork">
            <Connection type="Dedicated" />
          </LocalNetworkSiteRef>
        </ConnectionsToLocalNetwork>
      </Gateway>
    </VirtualNetworkSite>
    ```
3. Az xml-sémafájl létrehozása és konfigurálása után töltse fel a fájlt. Ezzel létrejön a virtuális hálózat.
   
    A fájl feltöltéséhez használja a következő parancsmagot, és cserélje le az értéket saját értékre.
   
    ```azurepowershell
    Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
    ```
4. <a name="gw"></a>Hozzon létre egy ExpressRoute-átjárót. Ne felejtse megadni a GatewaySKU paraméterben a *Standard*, a *HighPerformance* vagy az *UltraPerformance* értéket, a GatewayType paraméterben pedig a *DynamicRouting* értéket.
   
    Használja a következő mintát, és cserélje le az értékeket saját értékekre.

    ```azurepowershell
    New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
    ```
5. Csatlakoztassa az ExpressRoute-átjárót az ExpressRoute-kapcsolatcsoporthoz. A lépés végrehajtásával létrejön a kapcsolat a helyszíni hálózat és az Azure között az ExpressRoute-on keresztül.
   
    ```azurepowershell
    New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
    ```
6. <a name="vpngw"></a>Ezután hozza létre a helyek közötti VPN-átjárót. A GatewaySKU paraméterben a *Standard*, a *HighPerformance* vagy az *UltraPerformance* értéket, a GatewayType paraméterben pedig a *DynamicRouting* értéket kell megadnia.
   
    ```azurepowershell
    New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
    ```
   
    A virtuális hálózati átjáró beállításainak, köztük az átjáróazonosítónak és a nyilvános IP-címnek a lekéréséhez használja a `Get-AzureVirtualNetworkGateway` parancsmagot.
   
    ```azurepowershell
    Get-AzureVirtualNetworkGateway
   
    GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
    GatewayName          : S2SVPN
    LastEventData        :
    GatewayType          : DynamicRouting
    LastEventTimeStamp   : 5/29/2015 4:41:41 PM
    LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
    LastEventID          : 23002
    State                : Provisioned
    VIPAddress           : 104.43.x.y
    DefaultSite          :
    GatewaySKU           : HighPerformance
    Location             :
    VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
    SubnetId             :
    EnableBgp            : False
    OperationDescription : Get-AzureVirtualNetworkGateway
    OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
    OperationStatus      : Succeeded
    ```
7. Hozzon létre egy helyi VPN-átjáró entitást. Ez a parancs nem konfigurálja a helyszíni VPN-átjárót. Ehelyett a helyi átjáró beállításai, például a nyilvános IP-cím és a helyszíni címtér megadására szolgál, hogy az Azure VPN-átjáró kapcsolódhasson hozzá.
   
   > [!IMPORTANT]
   > A netcfg nem határozza meg a helyek közötti VPN helyi helyét. Ez a parancsmag a helyi hely paramétereinek meghatározására szolgál. Ez egyik portálon és a netcfg-fájlon keresztül sem határozható meg.
   > 
   > 
   
    Használja a következő mintát, és cserélje le az értékeket saját értékekre.
   
    ```azurepowershell
    New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
    ```
   
   > [!NOTE]
   > Ha a helyi hálózat több útvonalat is tartalmaz, tömbként egyszerre mindet megadhatja.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    A virtuális hálózati átjáró beállításainak, köztük az átjáróazonosítónak és a nyilvános IP-címnek a lekéréséhez használja a `Get-AzureVirtualNetworkGateway` parancsmagot. Tekintse meg a következő példát.

    ```azurepowershell
    Get-AzureLocalNetworkGateway

    GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
    GatewayName          : MyLocalNetwork
    IpAddress            : 23.39.x.y
    AddressSpace         : {10.1.2.0/24}
    OperationDescription : Get-AzureLocalNetworkGateway
    OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
    OperationStatus      : Succeeded
    ```


1. Konfigurálja helyi VPN-eszközét, hogy az új átjáróhoz csatlakozzon. A VPN-eszköz konfigurálása során használja a 6. lépésben lekért információkat. A VPN-eszköz konfigurálásával kapcsolatos további információkért lásd: [VPN-eszköz konfigurálása](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
2. Csatlakoztassa az Azure-on a helyek közötti VPN-átjárót a helyi átjáróhoz.
   
    Ebben a példában a connectedEntityId a helyi átjáró, amely a `Get-AzureLocalNetworkGateway` futtatásával azonosítható. A virtualNetworkGatewayId a `Get-AzureVirtualNetworkGateway` parancsmag használatával azonosítható. A lépés után létrejön a kapcsolat a helyszíni hálózat és az Azure között a helyek közötti VPN-kapcsolaton keresztül.

    ```azurepowershell
    New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Egyidejű kapcsolatok konfigurálása meglévő VNet számára
Ha már rendelkezik meglévő virtuális hálózattal, ellenőrizze az átjáró-alhálózat méretét. Ha az átjáró-alhálózat /28 vagy /29, először törölnie kell a virtuális hálózati átjárót, és növelnie kell az átjáró-alhálózat méretét. A jelen szakaszban ismertetett lépések bemutatják, mindez hogyan valósítható meg.

Ha az átjáró-alhálózat /27 vagy nagyobb, és a virtuális hálózat ExpressRoute-on keresztül csatlakozik, kihagyhatja az alábbi lépéseket, és továbbléphet a [„6. lépés – Helyek közötti VPN-átjáró létrehozása”](#vpngw) lépésre az előző szakaszban.

> [!NOTE]
> Amikor törli a meglévő átjárót, megszakad a helyi helyszínek kapcsolata a virtuális hálózattal, amíg ezen a konfiguráción dolgozik.
> 
> 

1. Az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióit kell telepítenie. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/). Vegye figyelembe, hogy az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja. 
2. Törölje a meglévő ExpressRoute- vagy helyek közötti VPN-átjárót. Használja a következő parancsmagot, és cserélje le az értékeket saját értékekre.
   
    ```azurepowershell
    Remove-AzureVNetGateway –VnetName MyAzureVNET
    ```
3. Exportálja a virtuális hálózati sémát. Használja a következő PowerShell-parancsmagot, és cserélje le az értékeket saját értékekre.
   
    ```azurepowershell
    Get-AzureVNetConfig –ExportToFile "C:\NetworkConfig.xml"
    ```
4. Szerkessze a hálózat konfigurációs sémáját, hogy az átjáró-alhálózat /27 vagy egy rövidebb előtag legyen (például /26 vagy /25). Tekintse meg a következő példát. 
   
   > [!NOTE]
   > Ha már nincs elegendő IP-cím a virtuális hálózaton az átjáró-alhálózat méretének növeléséhez, további IP-címtereket kell hozzáadnia. Az új konfigurációs sémával kapcsolatos információkért lásd: [Azure Virtual Network konfigurációs séma](/previous-versions/azure/reference/jj157100(v=azure.100)).
   > 
   > 
   
    ```xml
    <Subnet name="GatewaySubnet">
      <AddressPrefix>10.17.159.224/27</AddressPrefix>
    </Subnet>
    ```
5. Ha az előző átjáró helyek közötti VPN volt, a kapcsolat típusát is át kell állítania **Dedicated** értékre.
   
    ```xml
    <Gateway>
      <ConnectionsToLocalNetwork>
        <LocalNetworkSiteRef name="MyLocalNetwork">
          <Connection type="Dedicated" />
        </LocalNetworkSiteRef>
      </ConnectionsToLocalNetwork>
    </Gateway>
    ```
6. Ezen a ponton egy átjáró nélküli VNettel rendelkezik. Új átjárók létrehozásához és a kapcsolatok véglegesítéséhez folytathatja az előző lépéssorban foglalt [4. lépés – ExpressRoute-átjáró létrehozása](#gw) lépéssel.

## <a name="next-steps"></a>Következő lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).