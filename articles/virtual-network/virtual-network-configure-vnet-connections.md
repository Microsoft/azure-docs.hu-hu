---
title: Virtuális hálózati vagy VPN-kapcsolatok konfigurálása és ellenőrzése
description: Részletes útmutató különböző Azure VPN-és virtuális hálózati telepítések konfigurálásához és ellenőrzéséhez
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9975e40f7d4f3b69c9281efd0288389740bf92ec
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943649"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Virtuális hálózati vagy VPN-kapcsolatok konfigurálása és ellenőrzése

Ez az útmutató részletes útmutatást nyújt a különböző Azure VPN-és virtuális hálózati telepítések konfigurálásához és ellenőrzéséhez. A forgatókönyvek közé tartozik például az átviteli útválasztás, a hálózatról a hálózatra irányuló kapcsolatok, a Border Gateway Protocol (BGP), a többhelyes kapcsolatok és a pont – hely kapcsolatok.

Az Azure VPN Gateways lehetővé teszi a rugalmasságot az Azure-ban szinte bármilyen csatlakoztatott virtuális hálózati topológia megszervezésében. Például a virtuális hálózatok összekapcsolhatók:

- Régiók között.
- A virtuális hálózati típusok (Azure Resource Manager és a klasszikus) között.
- Az Azure-ban vagy egy helyszíni hibrid környezeten belül.
- Különböző előfizetésekben. 

## <a name="network-to-network-vpn-connection"></a>Hálózat – hálózat közötti VPN-kapcsolat

A virtuális hálózatok VPN-kapcsolaton keresztüli másik virtuális hálózathoz (hálózat – hálózat) való csatlakoztatása hasonló a virtuális hálózat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus VPN-átjárót használ a biztonságos alagút biztosításához az IPsec és az IKE használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben.

![Hálózatok közötti hálózati kapcsolatok IPsec-sel](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Ha a virtuális hálózatok ugyanabban a régióban találhatók, érdemes lehet megfontolni a csatlakozást a virtuális hálózati kapcsolatok használatával. A virtuális hálózat társítása nem használ VPN-átjárót. Növeli az átviteli sebességet, és csökkenti a késést. A virtuális hálózati társ-összekapcsolási kapcsolatok konfigurálásához válassza a **VNet-társítás konfigurálása és érvényesítése** lehetőséget.

Ha a virtuális hálózatok az Azure Resource Manger-alapú üzemi modellel lettek létrehozva, válassza a **Resource Manager-VNet konfigurálása és érvényesítése Resource Manager-VNet kapcsolattal** a VPN-kapcsolat konfigurálását.

Ha a virtuális hálózatok egyike a klasszikus Azure üzemi modellel lett létrehozva, és a másik a Resource Managerrel lett létrehozva, akkor válassza a **klasszikus VNet konfigurálása és ellenőrzése Resource Manager VNet-kapcsolaton** lehetőséget a VPN-kapcsolat konfigurálásához.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Virtuális hálózati társítás konfigurálása két virtuális hálózat között ugyanabban a régióban

Mielőtt megkezdené az Azure-beli virtuális hálózatok társításának megvalósítását és konfigurálását, győződjön meg arról, hogy megfelel az alábbi előfeltételeknek:

* A virtuális társhálózatoknak ugyanabban az Azure-régióban kell lenniük.
* A társ virtuális hálózatoknak olyan IP-címtartomány szükségesek, amelyek nem fedik át egymást.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre. Nincs származtatott tranzitív kapcsolat a különböző társítások között. Ha például a Társviszonyban áll a b-mel van társítva, és a b a VNetC-vel van társítva, akkor a Társviszonyban áll *nem* kapcsolódik a VNetC.

A követelmények teljesítése után követheti az [oktatóanyagot: virtuális hálózatok összekapcsolásának engedélyezése a virtuális hálózatokkal a Azure Portal használatával](./tutorial-connect-virtual-networks-portal.md) a társítás létrehozásához és konfigurálásához.

A társítás konfigurációjának vizsgálatához használja a következő metódust:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions).
2. A portál felső részén található szöveges **keresési erőforrásokat** tartalmazó mezőbe írja be a következőt: **Virtual Networks (virtuális hálózatok**). Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
3. A megjelenő **Virtual Networks (virtuális hálózatok** ) panelen válassza ki azt a virtuális hálózatot, amelyhez létre kíván hozni egy társítást.
4. A virtuális hálózatban megjelenő ablaktáblán válassza a **Beállítások** **szakaszban található** társítások lehetőséget.
5. Válasszon ki egy társítást, és tekintse meg a konfigurációs eredményeket.

![A virtuális hálózat társítási konfigurációjának ellenőrzéséhez kiválasztott beállítások](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Azure PowerShell esetén futtassa a [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) parancsot a virtuális hálózat társításának beszerzéséhez. Bemutatunk egy példát:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Resource Manager-beli virtuális hálózat összekötése egy másik Resource Manager virtuális hálózattal

Az egyik Resource Manager-beli virtuális hálózatról egy másik Resource Manager-beli virtuális hálózathoz is beállíthat kapcsolatokat. Vagy az IPsec használatával is konfigurálhatja a kapcsolatokat.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>VPN-kapcsolat konfigurálása Resource Manager-alapú virtuális hálózatok között

Ha az IPsec nélküli Resource Manager-alapú virtuális hálózatok közötti kapcsolatot szeretne konfigurálni, tekintse meg a [hálózat – hálózat közötti VPN Gateway-kapcsolat konfigurálása a Azure Portal használatával](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)című témakört.

Ha két Resource Manager-alapú virtuális hálózat között szeretne IPsec-kapcsolatot konfigurálni, hajtsa végre az 1 – 5. lépést a [helyek közötti kapcsolat létrehozása a Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md) az egyes virtuális hálózatok esetében.

> [!Note]
> Ezek a lépések csak az azonos előfizetésben lévő virtuális hálózatok esetében működnek. Ha a virtuális hálózatok különböző előfizetésekben találhatók, a kapcsolódáshoz a PowerShellt kell használnia. Lásd a [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)-re vonatkozó cikket.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>A VPN-kapcsolat ellenőrzése Resource Manager-alapú virtuális hálózatok között

![Klasszikus virtuális hálózati kapcsolódás Azure Resource Manager virtuális hálózathoz](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

A VPN-kapcsolat megfelelő konfigurálásának ellenőrzéséhez kövesse az alábbi utasításokat.

> [!Note] 
> A fenti lépésekben szereplő virtuális hálózati összetevők száma az előző ábrán szereplő számoknak felel meg.

1. Győződjön meg arról, hogy nincs átfedésben lévő címterület a csatlakoztatott virtuális hálózatokban.
2. Győződjön meg arról, hogy a Azure Resource Manager virtuális hálózat (1) címtartomány pontosan van definiálva a (4) **Kapcsolatfájl** -példányban.
3. Győződjön meg arról, hogy a Azure Resource Manager virtuális hálózat (6) címtartomány pontosan van definiálva a (3) **kapcsolattípus** -példányban.
4. Ellenőrizze, hogy az előmegosztott kulcsok megfelelnek-e a kapcsolatok objektumainak.
5. Győződjön meg arról, hogy a virtuális hálózati átjáró (2) Azure Resource Manager pontosan van definiálva a (4) **Kapcsolatfájl** -példányban.
6. Győződjön meg arról, hogy a virtuális hálózati átjáró (5) Azure Resource Manager pontosan van definiálva a (3) **kapcsolattípus** -példányban.

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Klasszikus virtuális hálózat összekötése Resource Manager-alapú virtuális hálózattal

Létrehozhat egy kapcsolatot a különböző előfizetésekben és különböző régiókban található virtuális hálózatok között. Emellett olyan virtuális hálózatokat is összekapcsolhat, amelyek már rendelkeznek kapcsolattal a helyszíni hálózatokkal, feltéve, hogy az átjáró típusát Route-alapúként konfigurálta.

Egy klasszikus virtuális hálózat és egy Resource Manager-alapú virtuális hálózat közötti kapcsolat konfigurálásához lásd: [a virtuális hálózatok összekapcsolása különböző üzemi modellekkel a Azure Portal használatával](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

![A Azure Resource Manager virtuális hálózathoz klasszikus virtuális hálózati kapcsolatokat bemutató diagram.](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Ha a klasszikus virtuális hálózat Azure Resource Manager virtuális hálózathoz való összekapcsolásakor szeretné megtekinteni a konfigurációt, kövesse az alábbi utasításokat.

> [!Note] 
> A fenti lépésekben szereplő virtuális hálózati összetevők száma az előző ábrán szereplő számoknak felel meg. 

1. Győződjön meg arról, hogy nincs átfedésben lévő címterület a csatlakoztatott virtuális hálózatokban.
2. Ellenőrizze, hogy a Azure Resource Manager virtuális hálózat (6) címtartomány pontosan van-e meghatározva a klasszikus helyi hálózat definíciójában (3).
3. Győződjön meg arról, hogy a klasszikus virtuális hálózat (1) címtartomány pontosan van definiálva a Azure Resource Manager a következőhöz: **kapcsolatbiztonsági** példány (4).
4. Győződjön meg arról, hogy a klasszikus virtuális hálózati átjáró (2) pontosan van definiálva a Azure Resource Manager a következőhöz: **kapcsolatbiztonsági** példány (4).
5. Győződjön meg arról, hogy a virtuális hálózati átjáró (5) Azure Resource Manager pontosan van definiálva a klasszikus **helyi hálózati definíciós** példányban (3).
6. Ellenőrizze, hogy az előmegosztott kulcsok megfelelőek-e a csatlakoztatott virtuális hálózatokon:
   - Klasszikus virtuális hálózat: **helyi hálózat definíciója** (3)
   - Azure Resource Manager virtuális hálózat: **kapcsolatok objektum** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Pont-hely típusú VPN-kapcsolat létrehozása

A pont – hely kapcsolat (a következő ábrán látható *P2S* ) konfigurálásával biztonságos kapcsolat hozható létre egy adott ügyfélszámítógépről egy virtuális hálózatra. A pont – hely kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy konferenciáról. Emellett akkor is hasznosak, ha csak néhány ügyfelet szeretne virtuális hálózathoz csatlakozni. 

A pont – hely VPN-kapcsolat az ügyfélszámítógépről a natív Windows VPN-ügyfélen keresztül kezdeményezhető. Az ügyfelek hitelesítése a kapcsolódáskor tanúsítványokkal történik.

![Pont–hely kapcsolat](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

A pont – hely kapcsolatokhoz nem szükséges VPN-eszköz. A VPN-kapcsolat létrehozása a Secure Socket Tunneling Protocol (SSTP) protokollal történik. A pont – hely kapcsolat egy virtuális hálózathoz különböző üzembe helyezési eszközök és üzembe helyezési modellek használatával kapcsolódhat:

* [Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz a Azure Portal használatával](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz a Azure Portal (klasszikus) használatával](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz a PowerShell használatával](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>Pont – hely kapcsolat ellenőrzése

A cikk [hibaelhárítása: az Azure pont – hely kapcsolati problémák](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) a pont – hely kapcsolatokkal kapcsolatos gyakori problémákon mennek keresztül.

## <a name="create-a-multisite-vpn-connection"></a>Többhelyes VPN-kapcsolat létrehozása

A helyek közötti kapcsolat (*S2S* a következő ábrán) használatával hozzáadhat egy olyan virtuális hálózathoz, amely már rendelkezik helyek közötti kapcsolattal, pont – hely kapcsolattal vagy hálózat – hálózat kapcsolattal. Ezt a fajta kapcsolatot gyakran *többhelyes* konfigurációnak nevezik. 

![Többhelyes kapcsolat](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Az Azure jelenleg két üzemi modellt használ: a Resource Manager-alapú és a klasszikus modellt. A két modell nem teljesen kompatibilis egymással. Ha többhelyes kapcsolatokat szeretne konfigurálni különböző modellekkel, tekintse meg a következő cikkeket:

* [Helyek közötti kapcsolat hozzáadása egy virtuális hálózathoz meglévő VPN Gateway-kapcsolattal](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [Helyek közötti kapcsolat hozzáadása egy virtuális hálózathoz meglévő VPN Gateway-kapcsolattal (klasszikus)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> Az ezekben a cikkekben szereplő lépések nem vonatkoznak az Azure ExpressRoute és a helyek közötti meglévő kapcsolati konfigurációkra. További információ: [ExpressRoute és helyek közötti egyidejű kapcsolatok](../expressroute/expressroute-howto-coexist-resource-manager.md).

## <a name="configure-transit-routing"></a>Tranzit útválasztás konfigurálása

A tranzit útválasztás egy adott útválasztási forgatókönyv, amelyben több hálózat is csatlakoztatható egy Daisy láncú topológiában. Ez az Útválasztás lehetővé teszi, hogy a lánc végén lévő virtuális hálózatok erőforrásai egymással kommunikáljanak egymással a (z) közötti virtuális hálózatok használatával. Az átirányítási útválasztás nélkül a hub-on keresztül megjelenő hálózatok vagy eszközök nem tudnak egymáshoz jutni.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Tranzit útválasztás konfigurálása pont – hely kapcsolaton

Képzelje el, hogy egy helyek közötti VPN-kapcsolatot szeretne konfigurálni a Társviszonyban áll és a b között. Emellett egy pont – hely típusú VPN-t is konfigurálnia kell az ügyfél számára a Társviszonyban áll átjáróhoz való csatlakozáshoz. Ezt követően engedélyezni szeretné a pont – hely típusú ügyfelek számára a b-hez való kapcsolódáshoz szükséges tranzit útválasztást, amely áthalad a Társviszonyban áll. 

Ez a forgatókönyv akkor támogatott, ha a BGP engedélyezve van a helyek közötti VPN-en a Társviszonyban áll és a b között. További információ: [a pont – hely VPN-útválasztás](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>ExpressRoute-kapcsolat átviteli útvonalának konfigurálása

Az Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy dedikált privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute-tal kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, a Microsoft 365-tel és a Dynamics 365-tel. További információkat az [ExpressRoute áttekintésében](../expressroute/expressroute-introduction.md) találhat.

![ExpressRoute az Azure-beli virtuális hálózatokhoz](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Azt javasoljuk, hogy ha a Társviszonyban áll és a b ugyanabban a geopolitikai régióban vannak, akkor a [virtuális hálózatokat a ExpressRoute-áramkörhöz](../expressroute/expressroute-howto-linkvnet-arm.md) is csatlakoztathatja az átviteli útválasztás konfigurálása helyett. Ha a virtuális hálózatok különböző geopolitikai régiókban találhatók, akkor közvetlenül is összekapcsolhatja őket az áramkörhöz, ha [ExpressRoute Premium](../expressroute/expressroute-faqs.md#expressroute-premium)van. 

Ha a ExpressRoute és a helyek közötti párhuzamos tárolással rendelkezik, a tranzit útválasztás nem támogatott. További információ: a [ExpressRoute és a helyek közötti kapcsolat konfigurálása a PowerShell használatával](../expressroute/expressroute-howto-coexist-resource-manager.md).

Ha engedélyezte a ExpressRoute számára a helyi hálózatok Azure-beli virtuális hálózathoz való összekapcsolását, akkor engedélyezheti a társítást azok között a virtuális hálózatok között, amelyeken át szeretné irányítani az átviteli útválasztást. Ha engedélyezni szeretné a helyi hálózatok számára a távoli virtuális hálózathoz való kapcsolódást, konfigurálnia kell a [virtuális hálózati](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)társítást. 

> [!Note]
> A virtuális hálózat társítása csak az azonos régióban található virtuális hálózatok esetében érhető el.

Kövesse az alábbi utasításokat, hogy meggyőződjön arról, hogy konfigurálta-e az átviteli útválasztást a virtuális hálózatokhoz:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions).
2. [Hozzon létre egy társítást a társviszonyban áll és a b között](./tutorial-connect-virtual-networks-portal.md) a korábbi ábrán látható módon. 
3. A virtuális hálózatban megjelenő ablaktáblán válassza a **Beállítások** **szakaszban található** társítások lehetőséget.
4. Válassza ki a megtekinteni kívánt társat. Ezután válassza a **konfiguráció** elemet annak ellenőrzéséhez, hogy engedélyezve van-e az **átjáró továbbítása** az ExpressRoute-áramkörhöz csatlakozó társviszonyban áll-hálózaton, és használja a távoli **átjárót** a ExpressRoute áramkörhöz nem csatlakozó távoli b-hálózaton.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Átviteli útválasztás konfigurálása virtuális hálózati társ-létrehozási kapcsolaton belül

Ha két virtuális hálózat között társhálózati viszony van, a virtuális társhálózatban működő átjáró is konfigurálható átviteli pontként a helyi hálózat felé. A virtuális hálózati társítások átviteli útvonalának konfigurálásához lásd: [hálózat – hálózati kapcsolatok](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!Note]
> Az átjáró átvitele nem támogatott a különböző üzembe helyezési modelleken keresztül létrehozott virtuális hálózatok közötti társítási kapcsolaton. A társítási kapcsolaton belül mindkét virtuális hálózatot létre kell hozni a Resource Manageren keresztül az átjáró működéséhez.

Kövesse az alábbi utasításokat, hogy meggyőződjön arról, hogy konfigurált-e átviteli útvonalat a virtuális hálózatokhoz:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions).
2. A portál felső részén található szöveges **keresési erőforrásokat** tartalmazó mezőbe írja be a következőt: **Virtual Networks (virtuális hálózatok**). Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
3. A megjelenő **Virtual Networks (virtuális hálózatok** ) panelen válassza ki azt a virtuális hálózatot, amelynek meg szeretné nézni a társítási beállításait.
4. A kiválasztott virtuális hálózathoz megjelenő ablaktáblán válassza a **Beállítások** **szakaszban található** társítások lehetőséget.
5. Válassza ki a megtekinteni kívánt társítást. Ellenőrizze, hogy engedélyezve van-e az **átjárók átvitelének engedélyezése** és **távoli átjárók használata** a **konfiguráció** alatt.

![Kiválasztás annak ellenőrzéséhez, hogy van-e átviteli útvonal konfigurálva a virtuális hálózatokhoz](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Átviteli útválasztás konfigurálása hálózat – hálózat kapcsolatba

A virtuális hálózatok közötti átviteli útválasztás konfigurálásához engedélyeznie kell a BGP-t az összes köztes hálózat – hálózat kapcsolathoz a Resource Manager-alapú üzemi modell és a PowerShell használatával. Útmutatásért lásd: a [BGP konfigurálása az Azure VPN Gatewayn a PowerShell használatával](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Az Azure VPN Gateway átjárón keresztüli átmenő forgalom a klasszikus üzemi modellen keresztül lehetséges, de a hálózati konfigurációs fájlban a statikusan definiált címekre támaszkodik. A BGP még nem támogatott az Azure Virtual Networks és a VPN Gateway használatával a klasszikus üzemi modellen keresztül. A BGP nélkül az átviteli címterület manuális definiálása hibás, ezért nem ajánlott.

> [!Note]
> A klasszikus hálózat – hálózat kapcsolatokat a klasszikus Azure portál használatával, vagy a klasszikus portál hálózati konfigurációs fájljának használatával konfigurálhatja. Klasszikus virtuális hálózat nem hozható létre és nem módosítható a Azure Resource Manager üzemi modell vagy a Azure Portal használatával. A klasszikus virtuális hálózatok tranzit útválasztásával kapcsolatos további információkért tekintse meg a [Microsoft fejlesztői blogját](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Az átviteli útválasztás konfigurálása helyek közötti kapcsolaton keresztül

A helyszíni hálózat és a helyek közötti kapcsolattal rendelkező virtuális hálózat közötti átviteli útválasztás konfigurálásához engedélyeznie kell a BGP-t az összes köztes helyek közötti kapcsolathoz a Resource Manager-alapú üzemi modell és a PowerShell használatával. Tekintse [meg, hogyan konfigurálhatja a BGP-t az Azure VPN Gateways szolgáltatásban a PowerShell használatával](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) útmutatásért.

Az Azure VPN Gateway átjárón keresztüli átmenő forgalom a klasszikus üzemi modellen keresztül lehetséges, de a hálózati konfigurációs fájlban a statikusan definiált címekre támaszkodik. A BGP még nem támogatott az Azure Virtual Networks és a VPN Gateway használatával a klasszikus üzemi modellen keresztül. A BGP nélkül az átviteli címterület manuális definiálása hibás, ezért nem ajánlott.

> [!Note]
> A klasszikus helyek közötti kapcsolatokat a klasszikus Azure portálon, vagy a klasszikus portálon található hálózati konfigurációs fájl használatával konfigurálhatja. Klasszikus virtuális hálózat nem hozható létre és nem módosítható a Azure Resource Manager üzemi modell vagy a Azure Portal használatával. A klasszikus virtuális hálózatok tranzit útválasztásával kapcsolatos további információkért tekintse meg a [Microsoft fejlesztői blogját](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

## <a name="configure-bgp-for-a-vpn-gateway"></a>BGP konfigurálása egy VPN-átjáró számára

A BGP az interneten használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információk két vagy több hálózat közötti cseréjéhez. Ha a BGP az Azure Virtual Networks környezetében használatos, lehetővé teszi az Azure VPN Gateway és a helyszíni VPN-eszközök, más néven BGP-társak vagy szomszédok használatát. Ők cserélik az "útvonalakat", amely tájékoztatja mindkét átjárót az előtagok rendelkezésre állásáról és elérhetőségéről, hogy átmenjen az érintett átjárók vagy útválasztók között. 

A BGP a több hálózat között is lehetővé teszi az átviteli útválasztást olyan útvonalak propagálásával, amelyekkel a BGP-átjáró egy BGP-társtól megtanulja az összes többi BGP-társat. További információ: [a BGP és az Azure VPN Gateway áttekintése](../vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="configure-bgp-for-a-vpn-connection"></a>A BGP konfigurálása VPN-kapcsolathoz

A BGP-t használó VPN-kapcsolat konfigurálásával kapcsolatban lásd: a [BGP konfigurálása Azure VPN Gateway-átjárón a PowerShell használatával](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Engedélyezze a BGP-t a virtuális hálózati átjárón egy autonóm rendszer (AS) számának létrehozásával. Az alapszintű átjárók nem támogatják a BGP-t. Az átjáró SKU-jának vizsgálatához nyissa meg a Azure Portal **VPN Gateway** paneljének **Áttekintés** szakaszát. Ha az SKU **alapszintű**, akkor módosítania kell az SKU-t (lásd: [az átjáró átméretezése](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?viewFallbackFrom=azurermps-4.0.0)) a **VpnGw1**. 

Az SKU ellenőrzése 20 – 30 perces állásidőt eredményez. Amint az átjáró megfelelő SKU-val rendelkezik, az AS-számot a [set-AzureRmVirtualNetworkGateway PowerShell-](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) parancsmagot használatával adhatja hozzá. A AS szám konfigurálása után a rendszer automatikusan megadja a BGP-társ IP-címet az átjáró számára.

Manuálisan kell megadnia az `LocalNetworkGateway` as-számot és a BGP-társ-címeket. A `ASN` és az `-BgpPeeringAddress` értékek a [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway) vagy a [set-AzureRmLocalNetworkGateway PowerShell-](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway) parancsmagot használatával állíthatók be. Egyes számok az Azure-hoz vannak fenntartva, és nem használhatók fel a BGP és az [azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)című témakörben leírtak szerint.

A kapcsolási objektumnak BGP-kompatibilisnek kell lennie. Az értéket beállíthatja a `-EnableBGP` `$True` [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) vagy a [set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection)értékre.

### <a name="validate-the-bgp-configuration"></a>A BGP-konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy a BGP megfelelően van-e konfigurálva, futtathatja a `get-AzureRmVirtualNetworkGateway` és a `get-AzureRmLocalNetworkGateway` parancsmagok. Ezután észreveheti a BGP-vel kapcsolatos kimenetet a `BgpSettingsText` részben. Például:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Magasan elérhető aktív/aktív VPN-kapcsolat létrehozása

Az aktív/aktív és aktív/készenléti átjárók közötti fő különbségek a következők:

* Két nyilvános IP-címmel rendelkező átjáró IP-konfigurációt kell létrehoznia.
* Be kell állítania a **EnableActiveActiveFeature** jelzőt.
* Az átjáró SKU-jának **VpnGw1**, **VpnGw2** vagy **VpnGw3** kell lennie.

Ha magas rendelkezésre állást szeretne biztosítani a létesítmények közötti és hálózatról hálózati kapcsolatokhoz, több VPN-átjárót kell üzembe helyeznie, és több párhuzamos kapcsolatot kell létesítenie a hálózatok és az Azure között. A kapcsolódási lehetőségek és a topológia áttekintését lásd: a [létesítmények közötti, illetve a hálózat – hálózat közötti kapcsolat](../vpn-gateway/vpn-gateway-highlyavailable.md).

Aktív/aktív létesítmények közötti és hálózat – hálózat típusú kapcsolatok létrehozásához kövesse az [Active/aktív S2S VPN-kapcsolatok konfigurálása Azure VPN Gateway](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) használatával az Azure VPN Gateway aktív/aktív módban való konfigurálásának utasításait.

> [!Note]  
> * Ha a BGP-kompatibilis aktív/aktív módban címeket ad hozzá a helyi hálózati átjáróhoz, *vegye fel csak a BGP-társak/32-címét*. Ha további címeket ad hozzá, akkor a rendszer statikus útvonalaknak számít, és elsőbbséget élvez a BGP-útvonalakkal szemben.
> * Az Azure-hoz csatlakozó helyszíni hálózatokhoz eltérő BGP-számokat kell használnia. (Ha ugyanazok, akkor a virtuális hálózatot számként kell módosítania, ha a helyszíni VPN-eszköz már használja az ASN-t a többi BGP-szomszédokkal.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Azure-beli VPN-átjáró típusának módosítása üzembe helyezés után

Az Azure-beli virtuális hálózati átjárók típusa nem módosítható házirend alapján, vagy közvetlenül útvonalon vagy más módon. Először törölnie kell az átjárót. Ezt követően a rendszer nem őrzi meg az IP-címet és az előmegosztott kulcsot. Ezután létrehozhat egy új átjárót a kívánt típushoz. 

Átjáró törléséhez és létrehozásához kövesse az alábbi lépéseket:

1. Törölje az eredeti átjáróhoz társított összes kapcsolatot.
2. Törölje az átjárót a Azure Portal, a PowerShell vagy a klasszikus PowerShell használatával: 
   * [Virtuális hálózati átjáró törlése a Azure Portal használatával](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [Virtuális hálózati átjáró törlése a PowerShell használatával](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Virtuális hálózati átjáró törlése a PowerShell (klasszikus) használatával](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. Kövesse a [VPN Gateway létrehozása](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway) a kívánt típus új átjárójának létrehozásához és a VPN-beállítás befejezéséhez című témakör lépéseit.

> [!Note]
> Ez a folyamat körülbelül 60 percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések

* [Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
