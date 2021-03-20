---
title: 'Azure ExpressRoute: kapcsolat ellenőrzése – hibaelhárítási útmutató'
description: Ez az oldal útmutatást nyújt a ExpressRoute-áramkör végpontok közötti kapcsolatának hibaelhárításához és ellenőrzéséhez.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204639"
---
# <a name="verifying-expressroute-connectivity"></a>Az ExpressRoute-kapcsolat ellenőrzése
Ez a cikk segítséget nyújt az ExpressRoute-kapcsolatok ellenőrzéséhez és hibakereséséhez. A ExpressRoute kiterjeszti a helyszíni hálózatot a Microsoft-felhőbe egy olyan privát kapcsolaton keresztül, amelyet általában egy kapcsolat szolgáltatója is megkönnyít. A ExpressRoute-kapcsolat hagyományosan három különálló hálózati zónát foglal magában, az alábbiak szerint:

-   Ügyfélhálózat
-   Szolgáltatói hálózat
-   Microsoft Datacenter

> [!NOTE]
> Az ExpressRoute közvetlen kapcsolati modellben (10/100 GB/s sávszélesség esetén) az ügyfelek közvetlenül kapcsolódhatnak a Microsoft Enterprise Edge (MSEE) útválasztók portjához. Ezért a közvetlen kapcsolati modellben csak az ügyfél és a Microsoft hálózati zónái érhetők el.
>


A dokumentum célja, hogy segítsen a felhasználónak azonosítani, hogy van-e kapcsolati probléma. Így a probléma megoldásához segítséget kérhet a megfelelő csapat támogatásában. Ha a probléma megoldásához Microsoft-támogatásra van szükség, nyisson meg egy támogatási jegyet [Microsoft ügyfélszolgálata][Support].

> [!IMPORTANT]
> A dokumentum célja, hogy segítséget nyújtson az egyszerű problémák diagnosztizálásában és javításában. Nem helyettesíti a Microsoft támogatási szolgálatát. Nyisson meg egy támogatási jegyet [Microsoft ügyfélszolgálata][Support] ha nem tudja megoldani a problémát a megadott útmutatás használatával.
>
>

## <a name="overview"></a>Áttekintés
Az alábbi ábrán egy ügyfél hálózatának logikai kapcsolata látható a Microsoft Network ExpressRoute használatával.
[![1]][1]

Az előző ábrán a számok a legfontosabb hálózati pontokat jelölik. Ezeket a hálózati pontokat a jelen cikkben, a hozzájuk tartozó szám szerint kell hivatkozni. A ExpressRoute kapcsolati modelljétől függően – a felhőalapú Exchange közös elhelyezési helye, pont-pont típusú Ethernet-kapcsolat vagy bármely-a-bármelyik (IPVPN) – a 3. és 4. hálózati pont lehet kapcsolók (2. rétegbeli eszközök) vagy útválasztók (3. rétegbeli eszközök). A közvetlen kapcsolati modellben nincsenek a 3. és 4. hálózati pontok; Ehelyett a CEs (2) közvetlenül a Msee keresztül csatlakozik a sötét szálhoz. A legfontosabb hálózati pontok a következők:

1.  Ügyfél-számítási eszköz (például kiszolgáló vagy számítógép)
2.  CEs: ügyfél peremhálózati útválasztói 
3.  PEs (CE felé irányuló): Provider Edge-útválasztók/kapcsolók, amelyek az ügyfél peremhálózati útválasztói felé néznek. A jelen dokumentum PE-CEs néven szerepel.
4.  PEs (MSEE szembenézve): Provider Edge-útválasztók/kapcsolók, amelyek a Msee felé néznek. A jelen dokumentum PE-MSEEs néven szerepel.
5.  Msee: Microsoft Enterprise Edge (MSEE) ExpressRoute útválasztók
6.  Virtual Network (VNet) átjáró
7.  Számítási eszköz az Azure VNet

Ha a felhőalapú Exchange közös helye, pont-pont típusú Ethernet vagy közvetlen kapcsolati modell van használatban, a CEs (2) létrehozza a BGP-társat a Msee (5). 

Ha a bármely (IPVPN) kapcsolati modellt használja, PE-MSEEs (4) a BGP-társak létrehozása a Msee (5) használatával. PE-MSEEs propagálja a Microsofttól kapott útvonalakat a IPVPN szolgáltató hálózatán keresztül az ügyfél hálózatának.

> [!NOTE]
>A magas rendelkezésre állás érdekében a Microsoft teljesen redundáns párhuzamos kapcsolatot létesít a Msee (5) és PE-MSEEs (4) párok között. A rendszer a teljes mértékben redundáns párhuzamos hálózati elérési utat is javasolja az ügyfél-hálózat és a PE-CEs pár között. A magas rendelkezésre állással kapcsolatos további információkért tekintse meg a [magas rendelkezésre állás tervezése a ExpressRoute][HA] -ben című cikket.
>
>

A következő logikai lépések a ExpressRoute áramkör hibaelhárítása című témakörben találhatók:

* [Az áramkör üzembe helyezésének és állapotának ellenőrzése](#verify-circuit-provisioning-and-state)
  
* [Egyenrangú konfiguráció ellenőrzése](#validate-peering-configuration)
  
* [ARP ellenőrzése](#validate-arp)
  
* [A BGP és az útvonalak ellenőrzése a MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [A forgalmi folyamat megerősítése](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Az áramkör üzembe helyezésének és állapotának ellenőrzése
Egy ExpressRoute áramkör kiépítésével redundáns 2. rétegbeli kapcsolatok hozhatók létre a CEs/PE-Msee (2)/(4) és a Msee (5) között. A ExpressRoute-áramkör létrehozásával, módosításával, kiépítésével és ellenőrzésével kapcsolatos további információkért tekintse meg a [ExpressRoute-áramkör létrehozása és módosítása][CreateCircuit]című cikket.

>[!TIP]
>A szolgáltatási kulcs egyedileg azonosít egy ExpressRoute-áramkört. Ha segítségre van szüksége a Microsofttól vagy egy ExpressRoute-partnertől a ExpressRoute kapcsolatos problémák megoldásához, adja meg a szolgáltatási kulcsot, hogy az áramkör könnyen azonosítható legyen.
>
>

### <a name="verification-via-the-azure-portal"></a>Ellenőrzés a Azure Portal használatával
A Azure Portal nyissa meg a ExpressRoute áramkör panelt. A panel ![3][3] szakaszában a ExpressRoute Essentials a következő képernyőképen látható módon jelenik meg:

![4][4]    

A ExpressRoute Essentialsben az áramkör *állapota* az áramkör állapotát jelzi a Microsoft oldalon. A *szolgáltató állapota* azt jelzi, hogy az áramkör üzembe helyezése */kiépítés nem* történt-e meg a szolgáltatói oldalon. 

Ahhoz, hogy egy ExpressRoute-áramkör működőképes legyen, *engedélyezni* kell az *áramköri állapotot* , és a *szolgáltatói állapotot* kell *kiépíteni*.

> [!NOTE]
> Ha a ExpressRoute áramkör konfigurálása után az *áramkör állapota* nem engedélyezett állapotú, forduljon a [Microsoft ügyfélszolgálatahoz][Support]. Ha viszont a *szolgáltató állapota* nem kiépített állapotban van, forduljon a szolgáltatóhoz.
>
>

### <a name="verification-via-powershell"></a>Ellenőrzés a PowerShell használatával
Egy erőforráscsoport összes ExpressRoute-áramkörének listázásához használja a következő parancsot:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Ha az erőforráscsoport nevét keresi, a *Get-AzResourceGroup* parancs használatával lekérheti az előfizetésben található összes erőforráscsoport listázásával.
>


Egy adott ExpressRoute-áramkör kiválasztásához használja az alábbi parancsot:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

A minta válasza:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

Annak ellenőrzéséhez, hogy a ExpressRoute áramkör működik-e, különös figyelmet fordít a következő mezőkre:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Ha a ExpressRoute áramkör konfigurálása után az *áramkör állapota* nem engedélyezett állapotú, forduljon a [Microsoft ügyfélszolgálatahoz][Support]. Ha viszont a *szolgáltató állapota* nem kiépített állapotban van, forduljon a szolgáltatóhoz.
>
>

## <a name="validate-peering-configuration"></a>Egyenrangú konfiguráció ellenőrzése
Miután a szolgáltató befejezte az ExpressRoute áramkör kiépítését, több eBGP-alapú útválasztási konfiguráció hozható létre a ExpressRoute áramkörön a CEs/MSEE-PEs (2)/(4) és a Msee (5) között. Minden ExpressRoute-áramkör rendelkezhet: Azure Private-kapcsolattal (az Azure-beli magánhálózati virtuális hálózatokra irányuló adatforgalom) és/vagy a Microsoft-partnerekkel (a Péter és SaaS nyilvános végpontokra irányuló forgalom). Az útválasztási konfiguráció létrehozásával és módosításával kapcsolatos további információkért tekintse meg a [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]című cikket.

### <a name="verification-via-the-azure-portal"></a>Ellenőrzés a Azure Portal használatával

> [!NOTE]
> A IPVPN-kapcsolati modellben a szolgáltatók kezelik a társítások konfigurálásának feladatait (3. rétegbeli szolgáltatások). Ebben a modellben, miután a szolgáltató konfigurált egy társítást, és ha a társítás üres a portálon, próbálja meg frissíteni az áramköri konfigurációt a portál frissítés gombjával. Ez a művelet lekéri az aktuális útválasztási konfigurációt az áramkörből. 
>

A Azure Portal egy ExpressRoute-áramköri társítás állapotát a ExpressRoute áramkör paneljén lehet ellenőrizni. A panel ![3][3] szakaszában a ExpressRoute-társítások a következő képernyőképen látható módon jelennek meg:

![5][5]

Az előző példában látható módon, ahogy az Azure Private-peering kiépítve van, míg az Azure nyilvános és a Microsoft-partnerek nincsenek kiépítve. A sikeresen kiépített társítási környezet az elsődleges és a másodlagos pont-pont típusú alhálózatokat is tartalmazza. A/30 alhálózatot a Msee és a CEs/PE-Msee kapcsolati IP-címéhez használja a rendszer. A kiépített társítások esetében a lista azt is jelzi, hogy ki utoljára módosította a konfigurációt. 

> [!NOTE]
> Ha a társítás engedélyezése sikertelen, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok megfelelnek-e a társított CE/PE-MSEE konfigurációjának. Ellenőrizze azt is, hogy a megfelelő *VlanId*, *AzureASN* és *PeerASN* használják-e a msee, és hogy ezek az értékek a társított CE/PE-MSEE használt értékekre vannak-e leképezve. Ha az MD5-kivonatolás van kiválasztva, a megosztott kulcsnak meg kell egyeznie a MSEE és a PE-MSEE/CE párokkal. A korábban konfigurált megosztott kulcs biztonsági okokból nem jelenik meg. Ha módosítania kell ezen konfiguráció bármelyikét egy MSEE-útválasztón, tekintse meg az [Útválasztás létrehozása és módosítása egy ExpressRoute áramkörhöz][CreatePeering]című témakört.  
>

> [!NOTE]
> A (z) illesztőfelülethez rendelt/30 alhálózaton a Microsoft az alhálózat második használható IP-címét fogja kiválasztani a MSEE felület számára. Ezért győződjön meg arról, hogy az alhálózat első felhasználható IP-címe hozzá lett rendelve a (z) alhálózathoz tartozó CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Ellenőrzés a PowerShell használatával
Az Azure Private peering konfigurációs adatainak beszerzéséhez használja a következő parancsokat:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

A sikeres konfiguráláshoz a következő a válasz:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 A sikeresen engedélyezett társítási környezet a felsorolt elsődleges és másodlagos címek előtagjai lennének. A/30 alhálózatot a Msee és a CEs/PE-Msee kapcsolati IP-címéhez használja a rendszer.

Az Azure nyilvános társ-összevonási konfiguráció részleteit az alábbi parancsokkal szerezheti be:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

A Microsoft társközi konfigurációs adatainak beszerzéséhez használja a következő parancsokat:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Ha nincs konfigurálva egy társítás, hibaüzenet jelenik meg. Egy példa válaszra, ha a megadott (Azure-beli nyilvános) kapcsolat nem az áramkörön belül van konfigurálva:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Ha a társítás engedélyezése sikertelen, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok megfelelnek-e a társított CE/PE-MSEE konfigurációjának. Ellenőrizze azt is, hogy a megfelelő *VlanId*, *AzureASN* és *PeerASN* használják-e a msee, és hogy ezek az értékek a társított CE/PE-MSEE használt értékekre vannak-e leképezve. Ha az MD5-kivonatolás van kiválasztva, a megosztott kulcsnak meg kell egyeznie a MSEE és a PE-MSEE/CE párokkal. A korábban konfigurált megosztott kulcs biztonsági okokból nem jelenik meg. Ha módosítania kell ezen konfiguráció bármelyikét egy MSEE-útválasztón, tekintse meg az [Útválasztás létrehozása és módosítása egy ExpressRoute áramkörhöz][CreatePeering]című témakört.  
>
>

> [!NOTE]
> A (z) illesztőfelülethez rendelt/30 alhálózaton a Microsoft az alhálózat második használható IP-címét fogja kiválasztani a MSEE felület számára. Ezért győződjön meg arról, hogy az alhálózat első felhasználható IP-címe hozzá lett rendelve a (z) alhálózathoz tartozó CE/PE-MSEE.
>

## <a name="validate-arp"></a>ARP ellenőrzése

Az ARP-táblázat egy adott társhoz tartozó IP-cím és MAC-cím hozzárendelését biztosítja. Az ExpressRoute-kezelői társítás ARP-táblázata az egyes interfészekhez (elsődleges és másodlagos) a következő információkat tartalmazza:
* Helyszíni útválasztó illesztő IP-címének hozzárendelése a MAC-címhez
* A ExpressRoute útválasztó felületének IP-címének hozzárendelése a MAC-címhez
* A leképezési ARP-táblázatok életkora segíthet a 2. rétegbeli konfiguráció érvényesítésében és az alapszintű 2. rétegbeli csatlakozási problémák elhárításában.


Lásd: az [ARP-táblák lekérése a Resource Manager-][ARP] alapú üzemi modell dokumentumában, a ExpressRoute-társítás ARP-táblázatának megtekintése, valamint az információ használata a 2. rétegbeli csatlakozási probléma megoldásához.


## <a name="validate-bgp-and-routes-on-the-msee"></a>A BGP és az útvonalak ellenőrzése a MSEE

Az alábbi paranccsal kérheti le az útválasztási táblázatot a MSEE a *magánhálózati* útválasztási környezet *elsődleges* elérési útjában:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Példa erre a válaszra:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Ha egy MSEE és egy CE/PE-MSEE közötti eBGP-társítás állapota aktív vagy üresjáratban van, ellenőrizze, hogy az elsődleges és a másodlagos társ-alhálózatok megfelelnek-e a társított CE/PE-MSEE konfigurációjának. Ellenőrizze azt is, hogy a megfelelő *VlanId*, *AzureAsn* és *PeerAsn* használják-e a msee, és ha ezek az értékek a csatolt PE-MSEE/CE-ben használt értékekre vannak leképezve. Ha az MD5-kivonatolás van kiválasztva, a megosztott kulcsnak azonosnak kell lennie a MSEE és a CE/PE-MSEE párban. Ha módosítania kell ezen konfiguráció bármelyikét egy MSEE-útválasztón, tekintse meg az [Útválasztás létrehozása és módosítása egy ExpressRoute áramkörhöz][CreatePeering]című témakört.
>


> [!NOTE]
> Ha bizonyos célhelyek nem érhetők el egy társításon keresztül, ellenőrizze a Msee útválasztási táblázatát a megfelelő társi környezetben. Ha az útválasztási táblázatban szerepel egy megfelelő előtag (lehet, hogy a (z) érték is található), akkor ellenőrizze, hogy vannak-e tűzfalak/NSG/ACL-ek a forgalmat blokkoló elérési úton.
>


A következő példa egy nem létező társ-létrehozási parancs válaszát mutatja be:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>A forgalmi folyamat megerősítése
A kombinált elsődleges és másodlagos elérési út forgalmi statisztikájának lekéréséhez a következő parancsot használhatja:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

A parancs mintájának kimenete:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

A nem létező társításhoz tartozó parancs mintájának kimenete a következő:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Következő lépések
További információért és segítségért tekintse meg az alábbi hivatkozásokat:

- [Microsoft támogatási szolgálat][Support]
- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logikai expressz útvonal-kapcsolat"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Minden erőforrás ikon"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Áttekintés ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "A ExpressRoute Essentials minta képernyőképe"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "A ExpressRoute Essentials minta képernyőképe"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md