---
title: A virtuális hálózatról az Azure-ra (nagyméretű példányok) való SAP HANAre beállított kapcsolat | Microsoft Docs
description: A virtuális hálózatról az Azure-ban (nagyméretű példányok) SAP HANA használatára beállított kapcsolat.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 540247752be27af268a0485ea9eb68d121a25240
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775305"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Virtuális hálózat összekapcsolása a HANA nagyméretű példányaival

Miután létrehozott egy Azure-beli virtuális hálózatot, ezt a hálózatot SAP HANA Azure-beli nagyméretű példányokon is összekapcsolhatja. Hozzon létre egy Azure ExpressRoute-átjárót a virtuális hálózaton. Ez az átjáró lehetővé teszi, hogy összekapcsolja a virtuális hálózatot ahhoz a ExpressRoute-áramkörhöz, amely az ügyfél bérlőhöz csatlakozik a HANA nagyméretű példány bélyegzőn.

> [!NOTE] 
> Ez a lépés akár 30 percet is igénybe vehet. A rendszer létrehozza az új átjárót a kijelölt Azure-előfizetésben, majd csatlakozik a megadott Azure-beli virtuális hálózathoz.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Ha egy átjáró már létezik, ellenőrizze, hogy a ExpressRoute-átjáró-e vagy sem. Ha nem ExpressRoute-átjáró, törölje az átjárót, és hozza létre újból ExpressRoute-átjáróként. Ha már létrejött egy ExpressRoute-átjáró, tekintse meg a cikk következő, "virtuális hálózatok csatolása" című szakaszát. 

- A [Azure Portal](https://portal.azure.com/) vagy a PowerShell használatával hozzon létre egy ExpressRoute VPN-átjárót a virtuális hálózathoz csatlakoztatva.
  - Ha a Azure Portal használja, vegyen fel egy új **Virtual Network-átjárót**, majd válassza a **ExpressRoute** lehetőséget az átjáró típusaként.
  - Ha a PowerShellt használja, először töltse le és használja a legújabb [Azure POWERSHELL SDK](https://azure.microsoft.com/downloads/)-t. 
 
Az alábbi parancsok ExpressRoute-átjárót hoznak létre. A által megelőzt szövegek a _$_ felhasználó által definiált változók, amelyeket frissíteni kell az adott információkkal.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Az Azure-ban (nagyméretű példányok) a SAP HANA egyetlen támogatott átjárói SKU- **UltraPerformance**.

## <a name="link-virtual-networks"></a>Virtuális hálózatok csatolása

Az Azure-beli virtuális hálózat most már rendelkezik egy ExpressRoute-átjáróval. A Microsoft által biztosított engedélyezési információk segítségével kapcsolja össze a ExpressRoute-átjárót a SAP HANA Large Instances ExpressRoute áramkörrel. A Azure Portal vagy a PowerShell használatával csatlakozhat. A PowerShell-utasítások a következők. 

Futtassa az alábbi parancsokat mindegyik ExpressRoute-átjáróhoz az egyes kapcsolatok eltérő AuthGUID használatával. A következő parancsfájlban látható első két bejegyzés a Microsoft által biztosított információból származik. Emellett a AuthGUID minden virtuális hálózat és az átjárója számára is egyedi. Ha egy másik Azure-beli virtuális hálózatot szeretne hozzáadni, egy másik AuthID kell beszereznie a ExpressRoute-áramkörhöz, amely a HANA Large-példányokat az Azure-ba csatlakoztatja a Microsofttól. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> A New-AzVirtualNetworkGatewayConnection parancs utolsó paramétere, a **ExpressRouteGatewayBypass** egy új paraméter, amely lehetővé teszi a ExpressRoute gyors elérési útját. Olyan funkció, amely csökkenti a HANA nagyméretű példány-egységek és az Azure-beli virtuális gépek közötti hálózati késést. A funkciók a 2019 májusában lettek hozzáadva. További részletekért lásd a [SAP HANA (nagyméretű példányok) hálózati architektúráját](./hana-network-architecture.md)ismertető cikket. A parancsok futtatása előtt győződjön meg arról, hogy a PowerShell-parancsmagok legújabb verzióját futtatja.

Ha az átjárót az előfizetéshez társított több ExpressRoute-áramkörhöz szeretné összekapcsolni, lehetséges, hogy ezt a lépést többször kell futtatnia. Előfordulhat például, hogy ugyanazt a virtuális hálózati átjárót fogja csatlakoztatni a ExpressRoute áramkörhöz, amely a virtuális hálózatot a helyszíni hálózathoz csatlakoztatja.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>ExpressRoute gyors elérési út alkalmazása meglévő HANA nagyméretű példányok ExpressRoute-áramkörökhöz
A dokumentáció eddig elmagyarázta, hogyan kapcsolódhat egy olyan új ExpressRoute-áramkörhöz, amelyet HANA nagyméretű példányú telepítéssel hoztak létre az Azure-beli virtuális hálózatok egyik Azure ExpressRoute-átjáróján. Számos ügyfél már rendelkezik a ExpressRoute-áramkörök beállításával, és a virtuális hálózatok már a HANA nagyméretű példányaihoz vannak csatlakoztatva. Mivel az új ExpressRoute gyors elérési útja csökkenti a hálózati késést, javasoljuk, hogy alkalmazza a módosítást a funkció használatára. Az új ExpreesRoute-kör és a meglévő ExpressRoute-áramkör módosításához szükséges parancsok megegyeznek. Ennek eredményeképpen futtatnia kell a PowerShell-parancsok ezen sorozatát egy meglévő kör használatára 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Fontos, hogy a fentiekben látható módon adja hozzá az utolsó paramétert a ExpressRoute gyors elérési útja funkciójának engedélyezéséhez.


## <a name="expressroute-global-reach"></a>Az ExpressRoute Global Reach
Ha engedélyezni szeretné a Global Reacht a két forgatókönyv egyikéhez vagy mindkettőhöz:

 - HANA rendszerreplikáció további proxyk vagy tűzfalak nélkül
 - Biztonsági másolatok másolása két különböző régióban lévő HANA nagyméretű példány-egységek között a rendszermásolatok vagy a rendszerfrissítések elvégzéséhez

a következőket kell figyelembe vennie:

- Meg kell adnia egy címtartomány-tartományt egy/29 címterület számára. Előfordulhat, hogy a címtartomány nem fedi át a többi olyan címtartományt, amelyet eddig használt a HANA nagyméretű példányainak az Azure-hoz való csatlakoztatásával, és előfordulhat, hogy az Azure-ban vagy a helyszínen használt IP-címtartományok egyike sem fedi át a tartományt.
- A ASN (autonóm rendszer száma) korlátozás vonatkozik arra, hogy a helyszíni útvonalakat a HANA nagyméretű példányokra hirdesse. A helyszíni szolgáltatás nem tehet közzé semmilyen, a 65000 – 65020 vagy 65515 tartományba tartozó privát ASN rendelkező útvonalat. 
- Ha a helyszíni közvetlen hozzáférést a HANA nagyméretű példányaihoz csatlakoztatja, ki kell számítania az Azure-hoz csatlakozó áramkör díját. Az árakért keresse [meg Global REACH-bővítmény](https://azure.microsoft.com/pricing/details/expressroute/)árát.

A telepítésre alkalmazott forgatókönyvek egyikének vagy mindkettőnek a megkereséséhez nyisson meg egy támogatási üzenetet az Azure-ban a következő témakörben leírtak szerint: [támogatási kérelem megnyitása HANA Large instances](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)

Az ehhez szükséges, a Microsoft számára a kéréshez használni kívánt, valamint a hozzájuk tartozó kulcsszavakat a következőképpen kell kinéznie:

- Szolgáltatás: SAP HANA nagyméretű példány
- Probléma típusa: konfiguráció és beállítás
- Probléma altípusa: a probléma nem szerepel a fentiekben
- "A hálózat módosítása – Global Reach hozzáadása" témakör tárgya
- Részletek: "Global Reach hozzáadása a HANA nagyméretű példányhoz a HANA nagyméretű példány-bérlőhöz vagy a" Global Reach hozzáadása a helyszínhez a nagyméretű példányok bérlője számára.
- További részletek a HANA nagyméretű példányának a HANA nagyméretű példány bérlői esetéhez: meg kell határoznia azt a **két Azure-régiót** , ahol a két bérlő csatlakozik, **és** el kell küldenie a **/29 IP-címtartományt** .
- További részletek a helyszíni és a HANA nagyméretű példányok bérlői esetéhez: meg kell határoznia azt az **Azure-régiót** , ahol a Hana nagyméretű példány bérlője telepítve van, közvetlenül szeretne csatlakozni. Emellett meg kell adnia a **hitelesítési GUID** azonosítót és az **áramköri társ-azonosítót** , amelyet a ExpressRoute áramkörnek a helyszíni és az Azure közötti létrehozásakor kapott. Emellett az **ASN** nevet kell megadnia. Az utolsó megszabadítható egy **/29 IP-címtartomány** a ExpressRoute Global REACH.

> [!NOTE]
> Ha mindkét esetet kezelni szeretné, két különböző/29 IP-címtartományt kell megadnia, amelyek nem fedik át az eddig használt egyéb IP-címtartományt. 




## <a name="next-steps"></a>Következő lépések

- [A HLI további hálózati követelményei](hana-additional-network-requirements.md)
