---
title: VPN-átjáróval történő adatátvitel konfigurálása virtuális hálózatok közötti társviszony létesítéséhez
description: Konfigurálja az átjárók átvitelét a virtuális hálózatok összevonásához, hogy a két Azure-beli virtuális hálózat zökkenőmentesen kapcsolódjon egymáshoz kapcsolati célokra.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872179"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>VPN-átjáróval történő adatátvitel konfigurálása virtuális hálózatok közötti társviszony létesítéséhez

Ez a cikk az átjáróval történő adatátvitel konfigurálásához nyújt segítséget a virtuális hálózatok közötti társviszony létesítéséhez. [A virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) lehetővé teszi két Azure-beli virtuális hálózat zökkenőmentes összekapcsolását, amely során a két virtuális hálózat csatlakozás szempontjából egyesítve lesz. Az [átjárók közötti átvitel](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) egy olyan egyenrangú tulajdonság, amely lehetővé teszi, hogy az egyik virtuális hálózat a VPN-átjárót a helyi vagy VNet közötti kapcsolathoz használja a virtuális hálózatban. Az alábbi ábrán látható, hogy működik az átjáróval történő átvitel a virtuális hálózatok közötti társviszony-létesítéssel.

![Átjáró átviteli diagramja](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Az ábrán látható átjáróval történő átvitel lehetővé teszi a virtuális társhálózatok számára az Azure VPN Gateway használatát a Hub-RM-ben. A VPN-átjárón elérhető csatlakozási lehetőségek, beleértve az S2S-, a P2S- és a virtuális hálózatok közötti kapcsolatokat, mindhárom virtuális hálózatra vonatkoznak. A továbbítási lehetőség elérhető az azonos vagy különböző üzemi modellek közötti társításhoz. Ha a különböző üzemi modellek közötti átvitelt konfigurálja, a hub virtuális hálózatának és a virtuális hálózati átjárónak a Resource Manager-alapú üzemi modellben kell lennie, nem a klasszikus üzemi modellben.
>

A küllős topológiájú hálózati architektúrában az átjáróval történő átvitel lehetővé teszi a küllőkön lévő virtuális hálózatok számára, hogy osztozzanak a középpontban található VPN-átjárón, így nem kell VPN-átjárót üzembe helyezni minden egyes küllőn lévő virtuális hálózatban. Az átjáróval összekötött virtuális hálózatokhoz vagy helyszíni hálózatokhoz vezető útvonalak átjáróval történő átvitellel a virtuális társhálózatok útválasztási tábláira propagálódnak. Az automatikus útvonal-propagálás letiltható a VPN-átjárókról. Hozzon létre egy útvonaltáblát a **BGP-útvonalpropagálás letiltása** beállítással, és társítsa az útvonaltáblát az alhálózatokhoz, hogy megakadályozza a szóban forgó alhálózatokra történő útvonalkiosztást. További információ: [Virtuális hálózat útvonaltáblája](../virtual-network/manage-route-table.md).

Ebben a cikkben két forgatókönyv van:

* **Azonos üzemi modell**: a rendszer mindkét virtuális hálózatot a Resource Manager-alapú üzemi modellben hozza létre.
* **Különböző üzembe helyezési modellek**: a küllős virtuális hálózat a klasszikus üzemi modellben jön létre, és a hub virtuális hálózat és az átjáró a Resource Manager-alapú üzemi modellben található.

>[!NOTE]
> Ha módosítja a hálózat topológiáját, és Windows VPN-ügyfeleket használ, a Windows-ügyfelekhez tartozó VPN-ügyfélszoftvert le kell tölteni és újra kell telepíteni ahhoz, hogy a módosítások érvénybe lépjenek az ügyfélen.
>

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következő virtuális hálózatokkal és engedélyekkel:

### <a name="virtual-networks"></a><a name="vnet"></a>Virtuális hálózatok

|VNet|Üzembehelyezési modell| Virtuális hálózati átjáró|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Igen](tutorial-create-gateway-portal.md)|
| Küllő – RM | [Resource Manager](./tutorial-site-to-site-portal.md)| No |
| Spoke-Classic | [Klasszikus](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | No |

### <a name="permissions"></a><a name="permissions"></a>Engedélyek

A virtuális hálózatok közötti társviszony létrehozásához használt fiókoknak rendelkezniük kell a megfelelő szerepkörökkel és engedélyekkel. Ha az alábbi példában a **hub-RM** és a **küllő-klasszikus** nevű két virtuális hálózat társítását választotta, a fióknak a következő szerepkörökkel vagy engedélyekkel kell rendelkeznie az egyes virtuális hálózatokhoz:

|VNet|Üzembehelyezési modell|Szerepkör|Engedélyek|
|---|---|---|---|
|Hub-RM|Resource Manager|[Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasszikus|[Klasszikus hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|Spoke-Classic|Resource Manager|[Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasszikus|[Klasszikus hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

További információ a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről (csak Resource Manager esetében).

## <a name="same-deployment-model"></a><a name="same"></a>Azonos üzemi modell

Ebben az esetben a virtuális hálózatok mind a Resource Manager-alapú üzemi modellben vannak. A következő lépésekkel hozhatja létre vagy frissítheti a virtuális hálózati társításokat az átjáró átvitelének engedélyezéséhez.

### <a name="to-add-a-peering-and-enable-transit"></a>Társítás hozzáadása és az átvitel engedélyezése

1. A [Azure Portal](https://portal.azure.com)hozza létre vagy frissítse a virtuális hálózat társítását a hub-RM-ből. Navigáljon a **hub-RM** virtuális hálózathoz. Válassza a **társak** lehetőséget, majd a **+ Hozzáadás** elemet a társítás **hozzáadásához**.
1. A **társ-Hozzáadás** lapon adja meg a **virtuális hálózat** értékeit.

   * Egyenrangú hivatkozás neve: nevezze el a hivatkozást. Példa: **HubRMToSpokeRM**
   * Távoli virtuális hálózatra irányuló forgalom: **Engedélyezés**
   * Távoli virtuális hálózatról továbbított forgalom: **Engedélyezés**
   * Virtuális hálózati átjáró: **a virtuális hálózat átjárójának használata**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Képernyőkép: a társítás hozzáadásának megjelenítése.":::

1. Ugyanazon a lapon folytassa a szolgáltatást a **távoli virtuális hálózat** értékeinek konfigurálásához.

   * Egyenrangú hivatkozás neve: nevezze el a hivatkozást. Példa: **SpokeRMtoHubRM**
   * Üzembe helyezési modell: **Resource Manager**
   * Virtual Network: **küllő – RM**
   * Távoli virtuális hálózatra irányuló forgalom: **Engedélyezés**
   * Távoli virtuális hálózatról továbbított forgalom: **Engedélyezés**
   * Virtuális hálózati átjáró: **a távoli virtuális hálózat átjárójának használata**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="A képernyőfelvétel megjeleníti a távoli virtuális hálózat értékeit.":::

1. A társítás létrehozásához válassza a **Hozzáadás** lehetőséget.
1. Ellenőrizze, hogy a társítási állapot mindkét virtuális hálózaton **csatlakoztatva** van-e.

### <a name="to-modify-an-existing-peering-for-transit"></a>Meglévő egymásra irányuló kapcsolat módosítása az átvitelhez

Ha a társítás már létrejött, módosíthatja a társítást az átvitelhez.

1. Navigáljon a virtuális hálózathoz. Válassza a társítások lehetőséget **, és válassza ki a** módosítani kívánt társítást.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Képernyőfelvétel: válassza a társítások lehetőséget.":::

1. Frissítse a VNet-társítást.

   * Távoli virtuális hálózatra irányuló forgalom: **Engedélyezés**
   * A virtuális hálózatra továbbított forgalom; **Engedélyezés**
   * Virtuális hálózati átjáró: **távoli virtuális hálózat átjárójának használata**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Képernyőfelvétel: a partneri átjáró módosítása.":::

1. A peering beállításainak **mentése** .

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell-minta

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

## <a name="different-deployment-models"></a><a name="different"></a>Különböző üzembe helyezési modellek

Ebben a konfigurációban a küllő VNet **küllő-klasszikus** a klasszikus üzemi modellben, a hub VNet **hub-RM** pedig a Resource Manager-alapú üzemi modellben található. A telepítési modellek közötti átvitel konfigurálásakor a virtuális hálózati átjárót a Resource Manager VNet kell konfigurálni, nem a klasszikus VNet.

Ehhez a konfigurációhoz csak a **hub-RM** virtuális hálózatot kell konfigurálnia. Semmit nem kell konfigurálnia a **küllős klasszikus** VNet.

1. A Azure Portal navigáljon a **hub-RM** virtuális hálózathoz **, válassza a** társítások lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget.
1. A **társ-Hozzáadás** lapon adja meg a következő értékeket:

   * Egyenrangú hivatkozás neve: nevezze el a hivatkozást. Példa: **HubRMToClassic**
   * Távoli virtuális hálózatra irányuló forgalom: **Engedélyezés**
   * Távoli virtuális hálózatról továbbított forgalom: **Engedélyezés**
   * Virtuális hálózati átjáró: **a virtuális hálózat átjárójának használata**
   * Távoli virtuális hálózat: **klasszikus**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="A klasszikus küllős oldal hozzáadása":::

1. Ellenőrizze az előfizetés helyességét, majd válassza ki a virtuális hálózatot a legördülő listából.
1. A társítás hozzáadásához válassza a **Hozzáadás** lehetőséget.
1. Ellenőrizze, hogy a társítási állapot a hub-RM virtuális hálózaton **csatlakoztatva** van-e. 

Ehhez a konfigurációhoz nem kell semmit konfigurálnia a **küllős klasszikus** virtuális hálózaton. Miután az állapot **csatlakoztatva** van, a küllős virtuális hálózat használhatja a VPN-átjárón keresztüli kapcsolatot a hub virtuális hálózatában.

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell-minta

A PowerShellt is használhatja a társviszony létrehozására és frissítésére a fenti példa segítségével. Cserélje le a változókat és az előfizetés azonosítóját a saját virtuális hálózatának, erőforráscsoportjainak és előfizetésének nevére. A virtuális hálózatok közötti társviszonyt csak a központi virtuális hálózaton kell létrehoznia.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg alaposabban a [virtuális hálózatok közötti társviszony-létesítés korlátozásaival és működésével](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints), valamint a [virtuális hálózatok közötti társviszonyok beállításaival](../virtual-network/virtual-network-manage-peering.md#create-a-peering), mielőtt virtuális hálózatok közötti társviszonyt hozna létre éles környezetben való használatra.
* Megtudhatja, [hogyan hozhat létre küllős hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) virtuális hálózatok közötti társviszony-létesítéssel és átjáróval történő átvitellel.
* [Hozzon létre virtuális hálózati társítást ugyanazzal a telepítési modellel](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Hozzon létre egy virtuális hálózati kapcsolatot különböző üzemi modellel](../virtual-network/create-peering-different-deployment-models.md).