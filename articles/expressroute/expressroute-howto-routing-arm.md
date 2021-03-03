---
title: 'Oktatóanyag: a ExpressRoute Circuit társításának konfigurálása – Azure PowerShell'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és kezelhet útválasztási konfigurációt egy ExpressRoute-áramkörhöz a Resource Manager-alapú üzemi modellben a PowerShell használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721939"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Oktatóanyag: társítások létrehozása és módosítása ExpressRoute-áramkörhöz a PowerShell használatával

Ez az oktatóanyag segítséget nyújt a ExpressRoute-áramkör útválasztási konfigurációjának létrehozásához és kezeléséhez a Resource Manager-alapú üzemi modellben a PowerShell használatával. A ExpressRoute-áramkör állapota, frissítése vagy törlése és megszüntetése is ellenőrizhető. Ha más módszert szeretne használni az áramkörrel való munkavégzéshez, válasszon egy cikket a következő listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társviszony-létesítés](about-public-peering.md)
> * [Videó – privát peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat kínál (jellemzően IPVPN, például MPLS), akkor a kapcsolati szolgáltató konfigurálja és kezeli az útválasztást.

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP-társítások konfigurálása előtt érdeklődjön a szolgáltatónál.
> 

A ExpressRoute-áramkörhöz privát és Microsoft-társítást is beállíthat (az Azure nyilvános társítása elavult az új áramkörök esetében). A társításokat tetszőleges sorrendben lehet konfigurálni. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományokról és a társításokról: [ExpressRoute-útválasztási tartományok](expressroute-circuit-peerings.md). A nyilvános hálózattal kapcsolatos további információkért lásd: [nyilvános ExpressRoute](about-public-peering.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Microsoft-társítások konfigurálása, frissítése és törlése egy áramkörhöz
> - Azure-beli privát kapcsolatok konfigurálása, frissítése és törlése egy áramkörhöz

## <a name="prerequisites"></a>Előfeltételek

* A konfigurálás megkezdése előtt győződjön meg arról, hogy áttekintette a következő lapokat:
    * [Előfeltételek](expressroute-prerequisites.md) 
    * [Útválasztási követelmények](expressroute-routing.md)
    * [Munkafolyamatok](expressroute-workflows.md)
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-arm.md) , és a folytatás előtt engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie ahhoz, hogy futtatni lehessen a parancsmagokat ebben a cikkben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-partnerek

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Microsoft-társi konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás-előtagokkal rendelkezik, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő. További információ: [útválasztási szűrő beállítása a Microsoft-társak](how-to-routefilter-powershell.md)számára.
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Jelentkezzen be, és válassza ki az előfizetését.

   Ha helyileg telepítette a PowerShellt, jelentkezzen be. Ha Azure Cloud Shell használ, kihagyhatja ezt a lépést.

   ```azurepowershell
   Connect-AzAccount
   ```

   Válassza ki azt az előfizetést, amelynek ExpressRoute-áramkörét létre kívánja hozni.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolat szolgáltatóját, hogy engedélyezze a Microsoft-partneri kapcsolatot. Nem kell követnie a következő szakaszokban felsorolt utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a konfigurációt a következő lépésekkel. 

3. Ellenőrizze a ExpressRoute áramkört, és győződjön meg arról, hogy az üzembe helyezése és engedélyezése is megtörténjen. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. A folytatás előtt győződjön meg arról, hogy rendelkezik a következő információkkal.

   * Az elsődleges hivatkozáshoz tartozó/30 vagy/126 alhálózat. A Címterület érvényes nyilvános IPv4-vagy IPv6-előtagnak kell lennie, és az Ön tulajdonában van, és egy RIR/IRR van regisztrálva.
   * A/30 vagy/126 alhálózat a másodlagos kapcsolathoz. A Címterület érvényes nyilvános IPv4-vagy IPv6-előtagnak kell lennie, és az Ön tulajdonában van, és egy RIR/IRR van regisztrálva.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: megtekintheti a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagokat szeretne elküldeni, vesszővel tagolt listát is küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt. Az IPv4 BGP-munkamenetek IPv4 meghirdetett előtagokat és IPv6 BGP-munkameneteket igényelnek az IPv6 meghirdetett előtagjaihoz. 
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Nem kötelező:
     * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nincsenek regisztrálva a társítás mint szám mezőben, megadhatja az AS-számot, amelyre regisztrálva van.
     * Egy MD5-kivonat, ha használni kívánja.

> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés sikertelen, a "AdvertisedPublicPrefixesState" a "Get-AzExpressRouteCircuitPeeringConfig" (lásd: "a Microsoft-partneri adatok beszerzése" című részben) "érvényesítés szükséges" értékkel jelenik meg. 
> 
> Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosaként listázott entitásban, és a támogatási jegy megnyitásával manuális ellenőrzés céljából elküldheti ezeket a dokumentumokat. 
> 

   A következő példa segítségével konfigurálja a Microsoft-társat az áramkörhöz:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek lekérése

A konfigurációs adatokat a következő példa használatával szerezheti be:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét frissítheti a következő példa használatával:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure-beli privát társhálózat-létesítés

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Azure-beli privát társ-összevonási konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

> [!IMPORTANT]
> A privát társítás IPv6-támogatása jelenleg **nyilvános előzetes** verzióban érhető el. 
> 
> 

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Importálja az ExpressRoute PowerShell-modulját.

   Telepítse a legújabb PowerShell-telepítőt [PowerShell-galériaról](https://www.powershellgallery.com/). Ezután importálja a Azure Resource Manager modulokat a PowerShell-munkamenetbe a ExpressRoute-parancsmagok használatának megkezdése érdekében. A PowerShellt rendszergazdaként kell futtatnia.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importálja az az összes. \* az ismert szemantikai verziószámozási tartományon belüli modulok.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Egy Select modult is importálhat az ismert szemantikai verziószámozási tartományon belül.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Jelentkezzen be a fiókjába.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Válassza ki azt az előfizetést, amelynek ExpressRoute-áramkörét létre kívánja hozni.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot.

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolati szolgáltatót, hogy engedélyezze az Azure-beli privát kapcsolatot. Nem kell követnie a következő szakaszokban felsorolt utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a konfigurációt a következő lépésekkel.

3. Ellenőrizze a ExpressRoute áramkört, és győződjön meg arról, hogy az üzembe helyezése és engedélyezése is megtörténjen. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. A következő lépések elvégzése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

   * Olyan alhálózatok párja, amelyek nem tartoznak a virtuális hálózatok számára fenntartott címtartomány részébe. A rendszer egy alhálózatot használ az elsődleges hivatkozáshoz, míg a másikat a másodlagos hivatkozáshoz fogja használni. Az egyes alhálózatokból az első használható IP-címet az útválasztóhoz rendeli hozzá, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz. Ezt az alhálózatot három lehetőséggel látja el:
       * IPv4: két/30 alhálózat.
       * IPv6: két/126 alhálózat.
       * Mindkettő: két/30 alhálózat és két/126 alhálózat.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Győződjön meg arról, hogy nem használja a 65515-et.
   * Nem kötelező:
     * Egy MD5-kivonat, ha használni kívánja.

   Az alábbi példát követve konfigurálhatja az Azure-beli privát kapcsolatot az áramkörhöz:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Az Azure Private peering részleteinek beszerzése

A konfigurációs adatokat a következő példa használatával érheti el:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét frissítheti a következő példán keresztül. Ebben a példában az áramkör VLAN-azonosítója 200 és 500 között frissül.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A következő parancsmag futtatásával távolíthatja el a társ-konfigurációt:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A következő példa futtatásával távolíthatja el a társ-konfigurációt:

> [!WARNING]
> A példa futtatása előtt győződjön meg arról, hogy a virtuális hálózatok és a ExpressRoute összes Global Reach kapcsolata el lesz távolítva. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az Azure Private-társítást, létrehozhat egy ExpressRoute-átjárót, amely összekapcsolja a virtuális hálózatot az áramkörrel. 

> [!div class="nextstepaction"]
> [Az ExpressRoute virtuális hálózati átjáróinak konfigurálása](expressroute-howto-add-gateway-resource-manager.md)
