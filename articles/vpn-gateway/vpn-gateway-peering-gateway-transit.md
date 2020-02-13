---
title: VPN-átjáróval történő adatátvitel konfigurálása virtuális hálózatok közötti társviszony létesítéséhez
description: Konfiguráljon VPN-átjáróval történő adatátvitelt virtuális hálózatok közötti társviszony létesítéséhez.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: 3917101ee7ac151cf624e5be0f51ccf01c8cb1cc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161885"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>VPN-átjáróval történő adatátvitel konfigurálása virtuális hálózatok közötti társviszony létesítéséhez

Ez a cikk az átjáróval történő adatátvitel konfigurálásához nyújt segítséget a virtuális hálózatok közötti társviszony létesítéséhez. [A virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) lehetővé teszi két Azure-beli virtuális hálózat zökkenőmentes összekapcsolását, amely során a két virtuális hálózat csatlakozás szempontjából egyesítve lesz. [Az átjáróval történő átvitel](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) olyan társviszony-létesítési tulajdonság, amely lehetővé teszi az egyik virtuális hálózat számára a VPN-átjáró használatát a virtuális társhálózaton belül a létesítmények vagy virtuális hálózatok közötti csatlakozáshoz. Az alábbi ábrán látható, hogy működik az átjáróval történő átvitel a virtuális hálózatok közötti társviszony-létesítéssel.

![gateway-transit](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Az ábrán látható átjáróval történő átvitel lehetővé teszi a virtuális társhálózatok számára az Azure VPN Gateway használatát a Hub-RM-ben. A VPN-átjárón elérhető csatlakozási lehetőségek, beleértve az S2S-, a P2S- és a virtuális hálózatok közötti kapcsolatokat, mindhárom virtuális hálózatra vonatkoznak. Az adatátvitel lehetőség elérhető azonos vagy eltérő üzemi modellek közötti társviszony-létesítéshez is. A megkötés az, hogy a VPN-átjáró csak a Resource Manager-alapú üzemi modellt használó virtuális hálózatban lehet, amint az ábrán látható.

A küllős topológiájú hálózati architektúrában az átjáróval történő átvitel lehetővé teszi a küllőkön lévő virtuális hálózatok számára, hogy osztozzanak a középpontban található VPN-átjárón, így nem kell VPN-átjárót üzembe helyezni minden egyes küllőn lévő virtuális hálózatban. Az átjáróval összekötött virtuális hálózatokhoz vagy helyszíni hálózatokhoz vezető útvonalak átjáróval történő átvitellel a virtuális társhálózatok útválasztási tábláira propagálódnak. Az automatikus útvonal-propagálás letiltható a VPN-átjárókról. Hozzon létre egy útvonaltáblát a **BGP-útvonalpropagálás letiltása** beállítással, és társítsa az útvonaltáblát az alhálózatokhoz, hogy megakadályozza a szóban forgó alhálózatokra történő útvonalkiosztást. További információ: [Virtuális hálózat útvonaltáblája](../virtual-network/manage-route-table.md).

Ez a dokumentum két forgatókönyvet mutat be:

1. Mindkét virtuális hálózat a Resource Manager-alapú üzemi modellt használja
2. A küllőn lévő virtuális hálózat klasszikus, az átjáróval rendelkező középponti virtuális hálózat pedig Resource Manager-alapú

## <a name="requirements"></a>Követelmények



A dokumentumban szereplő példához az alábbi erőforrásokat kell létrehozni:

1. Hub-RM (központi RM-alapú) virtuális hálózat VPN-átjáróval
2. Spoke-RM (Küllőn elhelyezkedő RM-alapú) virtuális hálózat
3. Spoke-Classic (Küllőn elhelyezkedő klasszikus) virtuális hálózat a klasszikus üzemi modellel
4. A használt fióknak rendelkeznie kell a szükséges szerepkörökkel és engedélyekkel. A részletekért tekintse meg a cikk [Engedélyek](#permissions) című szakaszát.

Útmutatásért lásd a következő dokumentumokat:

1. [VPN-átjáró létrehozása egy virtuális hálózatban](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Ugyanazon üzemi modellel működő virtuális hálózatok közötti társviszony-létesítés](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Eltérő üzemi modellel működő virtuális hálózatok közötti társviszony-létesítés](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>Engedélyek

A virtuális hálózatok közötti társviszony létrehozásához használt fiókoknak rendelkezniük kell a megfelelő szerepkörökkel és engedélyekkel. Az alábbi példában, ha a Hub-RM (központi RM-alapú) és a Spoke-Classic (küllőn elhelyezkedő klasszikus) nevű virtuális hálózatokat kapcsolja össze, a fióknak az alábbi szerepkörökkel vagy engedélyekkel kell rendelkeznie az egyes virtuális hálózatokon:
    
|Virtuális hálózat|Üzemi modell|Szerepkör|Engedélyek|
|---|---|---|---|
|Hub-RM|Resource Manager|[Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasszikus|[Klasszikus hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|Spoke-Classic|Resource Manager|[Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasszikus|[Klasszikus hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

További információ a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) és a bizonyos engedélyek [egyéni szerepkörökhöz](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről (csak Resource Manager esetében).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Resource Manager és Resource Manager közötti társviszony-létesítés átjáróval történő átvitellel

Kövesse a virtuális hálózatok közötti társviszony létrehozására vagy frissítésére vonatkozó utasításokat az átjáróval történő átvitel engedélyezéséhez.

1. Hozza létre vagy frissítse a virtuális hálózatok közötti, a Spoke-RM felől a Hub-RM irányába mutató társviszonyt az Azure Portalon. Navigáljon a Spoke-RM nevű virtuális hálózati előforráshoz, és kattintson a Társviszonyok, majd a Hozzáadás elemre:
    - Állítsa be a Resource Manager beállítást
    - Válassza ki a Hub-RM nevű virtuális hálózatot a megfelelő előfizetésben
    - Győződjön meg róla, hogy a Hálózati hozzáférés engedélyezése beállítás Engedélyezve értékű.
    - Állítsa be a **Távoli átjárók használata** beállítást
    - Kattintson az OK gombra.

      ![spokerm-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Ha a társviszony már létrejött, keresse meg a tárviszony-létesítési erőforrást, majd engedélyezze a **Távoli átjárók használata** beállítást az 1. lépésben szereplő képernyőképen látható módon

3. Hozza létre vagy frissítse a virtuális hálózatok közötti, a Hub-RM felől a Spoke-RM irányába mutató társviszonyt az Azure Portalon. Navigáljon a Hub-RM nevű virtuális hálózati előforráshoz, és kattintson a Társviszonyok, majd a Hozzáadás elemre:
    - Állítsa be a Resource Manager beállítást
    - Győződjön meg róla, hogy a Hálózati hozzáférés engedélyezése beállítás Engedélyezve értékű.
    - Válassza ki a Spoke-RM nevű virtuális hálózatot a megfelelő előfizetésben
    - Adja meg az **Átjáróforgalom engedélyezése** beállítást
    - Kattintson az OK gombra.

      ![hubrm-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Ha a társviszony már létrejött, keresse meg a társviszony-létesítési erőforrást, majd engedélyezze az **Átjáróforgalom engedélyezése** beállítást a 3. lépésben szereplő képernyőképen látható módon.

5. Ellenőrizze, hogy a társviszony-létesítés mindkét virtuális hálózaton **Csatlakoztatva** állapotú-e

### <a name="powershell-sample"></a>PowerShell-minta

A PowerShellt is használhatja a társviszony létrehozására és frissítésére a fenti példa segítségével. Cserélje le a változókat a saját virtuális hálózatainak és erőforráscsoportjainak nevére.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Klasszikus–Resource Manager-alapú társviszony-létesítés átjáróval történő átvitellel

A lépések hasonlóak a Resource Manager-alapú példához, azzal a kivétellel, hogy a műveletek csak a Hub-RM virtuális hálózaton lesznek alkalmazva.

1. Hozza létre vagy frissítse a virtuális hálózatok közötti, a Hub-RM felől a Spoke-RM irányába mutató társviszonyt az Azure Portalon. Navigáljon a Hub-RM nevű virtuális hálózati előforráshoz, és kattintson a Társviszonyok, majd a Hozzáadás elemre:
   - A virtuális hálózat üzemi modelljénél válassza a „Klasszikus” beállítást
   - Válassza ki a „Spoke-Classic” nevű virtuális hálózatot a megfelelő előfizetésben
   - Győződjön meg róla, hogy a Hálózati hozzáférés engedélyezése beállítás Engedélyezve értékű.
   - Adja meg az **Átjáróforgalom engedélyezése** beállítást
   - Kattintson az OK gombra.

     ![hubrm-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Ha a társviszony már létrejött, keresse meg a társviszony-létesítési erőforrást, majd engedélyezze az **Átjáróforgalom engedélyezése** beállítást az 1. lépésben szereplő képernyőképen látható módon.

3. Nincs művelet a Spoke-Classic nevű virtuális hálózaton

4. Ellenőrizze, hogy a társviszony-létesítés a Hub-RM virtuális hálózaton „**Csatlakoztatva**” állapotú-e

Amint megjelenik a „Csatlakoztatva” állapot, a küllőkön lévő virtuális hálózatok használatba vehetik a virtuális hálózatok vagy helyszínek közötti kapcsolatokat a központi virtuális hálózatban található VPN-átjárón keresztül.

### <a name="powershell-sample"></a>PowerShell-minta

A PowerShellt is használhatja a társviszony létrehozására és frissítésére a fenti példa segítségével. Cserélje le a változókat és az előfizetés azonosítóját a saját virtuális hálózatának, erőforráscsoportjainak és előfizetésének nevére. A virtuális hálózatok közötti társviszonyt csak a központi virtuális hálózaton kell létrehoznia.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg alaposabban a [virtuális hálózatok közötti társviszony-létesítés korlátozásaival és működésével](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints), valamint a [virtuális hálózatok közötti társviszonyok beállításaival](../virtual-network/virtual-network-manage-peering.md#create-a-peering), mielőtt virtuális hálózatok közötti társviszonyt hozna létre éles környezetben való használatra.
* Megtudhatja, [hogyan hozhat létre küllős hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) virtuális hálózatok közötti társviszony-létesítéssel és átjáróval történő átvitellel.
