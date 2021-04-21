---
title: Azure-beli virtuális gépek útválasztási problémájának diagnosztizálása | Microsoft Docs
description: Megtudhatja, hogyan diagnosztizálhatja a virtuális gépek útválasztási problémáját a virtuális gép hatályos útvonalának megtekintésével.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: f84b74b054a073f2c1ae5ba2ac7d0d0a968367c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767672"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Virtuális gép útválasztási problémájának diagnosztizálása

Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja az útválasztási problémákat a virtuális gépek hálózati adaptere számára hatékony útvonalak megtekintésével. Az Azure több alapértelmezett útvonalat hoz létre minden egyes virtuális hálózati alhálózathoz. Felülbírálhatja az Azure alapértelmezett útvonalait, ha útvonalakat definiál egy útvonaltáblában, majd társítja az útvonaltáblát egy alhálózathoz. A létrehozott útvonalak, az Azure alapértelmezett útvonalai, valamint a helyszíni hálózatról egy Azure VPN-átjárón keresztül propagált útvonalak (ha a virtuális hálózat a helyszíni hálózathoz csatlakozik) a Border Gateway Protocol (BGP) használatával propagált útvonalak együttesei az alhálózat összes hálózati adapterének érvényes útvonalai. Ha nem ismeri a virtuális hálózatokkal, a hálózati adapterekkel és [](virtual-network-network-interface.md)az útválasztással kapcsolatos fogalmakat, tekintse meg a virtuális hálózatok áttekintésével, [](virtual-networks-overview.md)a hálózati adaptersel és az útválasztás [áttekintésével kapcsolatos témakört.](virtual-networks-udr-overview.md)

## <a name="scenario"></a>Eset

Megpróbál csatlakozni egy virtuális géphez, de a kapcsolat meghiúsul. Annak megállapításához, hogy miért nem tud csatlakozni a virtuális géphez, megtekintheti a hálózati adapterek hatályos útvonalait az [Azure](#diagnose-using-azure-portal)Portal, a [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI használatával.](#diagnose-using-azure-cli)

A következő lépések feltételezik, hogy már van egy virtuális gépe, amelyből megtekintheti a hatályos útvonalait. Ha még nincs virtuális gépe, először telepítsen egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális gépet a cikkben található feladatok elvégzéséhez. A cikkben található példák egy *myVM* nevű virtuális gépre mutatnak, amely egy *myVMNic1 nevű hálózati adaptert tartalmaz.* A virtuális gép és a hálózati adapter egy *myResourceGroup nevű erőforráscsoportban* található, és az USA keleti *régiójában* található. Szükség szerint módosítsa a lépésekben szereplő értékeket arra a virtuális gépre, amelyről diagnosztizálja a problémát.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálás a Azure Portal

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com) egy olyan Azure-fiókkal, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)
2. A virtuális gép Azure Portal írja be a futó állapotban lévő virtuális gép nevét a keresőmezőbe. Amikor a virtuális gép neve megjelenik a keresési eredmények között, válassza ki.
3. A **bal oldali** Beállítások alatt válassza a Hálózat **lehetőséget,** és a nevének kiválasztásával lépjen a hálózati adapter erőforrásához.
     ![Hálózati adapterek megtekintése](./media/diagnose-network-routing-problem/view-nics.png)
4. A bal oldalon válassza az Hatályos **útvonalak lehetőséget.** A **myVMNic1** nevű hálózati adapter hatályos útvonalai a következő képen láthatók: ![ Az hatályos útvonalak megtekintése](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Ha több hálózati adapter van csatlakoztatva a virtuális géphez, bármely hálózati adapter hatályos útvonalát megtekintheti annak kiválasztásával. Mivel az egyes hálózati adapterek eltérő alhálózatban is lehet, minden hálózati adapter különböző hatékony útvonalakkal is rendelkezik.

    Az előző képen látható példában a felsorolt útvonalak az azure által az egyes alhálózatok számára létrehozott alapértelmezett útvonalak. A lista legalább ezekkel az útvonalakkal rendelkezik, de előfordulhat, hogy további útvonalakkal is rendelkezik, attól függően, hogy milyen képességeket engedélyezett a virtuális hálózathoz, például hogy egy másik virtuális hálózattal van társviszonyban, vagy egy Azure VPN-átjárón keresztül csatlakozik a helyszíni hálózathoz. Az egyes útvonalakkal és a hálózati adapterhez esetleg látható egyéb útvonalakkal kapcsolatos további információkért lásd: Virtuális hálózati [forgalom útválasztása.](virtual-networks-udr-overview.md) Ha a lista sok útvonalat kínál, egyszerűbbnek találhatja a Letöltés lehetőséget, ha egy .csv-fájlt tölt le az útvonalak listájával. 

Bár az hatályos útvonalakat az előző lépésekben a virtuális gépen nézték meg, az hatályos útvonalakat a következőn keresztül is megtekintheti:
- **Egyéni hálózati adapter:** Útmutató [a hálózati adapterek megtekintéséhez.](virtual-network-network-interface.md#view-network-interface-settings)
- **Egyéni útvonaltábla:** Megtudhatja, hogyan [lehet megtekinteni egy útvonaltáblát.](manage-route-table.md#view-details-of-a-route-table)

## <a name="diagnose-using-powershell"></a>Diagnosztizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancsokat futtathatja a [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. A Azure Cloud Shell egy ingyenes interaktív felület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell 1.0.0-s vagy újabb verziójára. A telepített verzió megkereséhez futtassa a következőt `Get-Module -ListAvailable Az` a számítógépen: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a futtatásával jelentkezzen be az Azure-ba egy olyan fiókkal, amely rendelkezik `Connect-AzAccount` a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)

Lekérte egy hálózati adapter hatályos útvonalait a [Get-AzEffectiveRouteTable használatával.](/powershell/module/az.network/get-azeffectiveroutetable) Az alábbi példa lekérte egy *myVMNic1* nevű hálózati adapter hatályos útvonalait, amely egy *myResourceGroup nevű erőforráscsoportban található:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

A kimenetben visszaadott információkért lásd: [Útválasztás áttekintése.](virtual-networks-udr-overview.md) A kimenet csak akkor lesz visszaadva, ha a virtuális gép futó állapotban van. Ha a virtuális géphez több hálózati adapter is csatlakozik, áttekintheti az egyes hálózati adapterek hatályos útvonalait. Mivel az egyes hálózati adapterek eltérő alhálózatban is lehet, az egyes hálózati adapterek különböző hatékony útvonalakkal is rendelkezik. Ha továbbra is kommunikációs problémát lát, tekintse meg a további [diagnosztikát és](#additional-diagnosis) [szempontokat.](#considerations)

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatolva van, a következő parancsok a virtuális géphez csatolt összes hálózati adapterhez visszaadják az adatokat:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

A következő példához hasonló kimenetet kap:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Az előző kimenetben a hálózati adapter neve *myVMNic1.*

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálás az Azure CLI használatával

A következő parancsokat futtathatja a Azure Cloud Shell  [parancssori](https://shell.azure.com/bash)felületének a számítógépről való futtatásával. Ehhez a cikkhez az Azure CLI 2.0.32-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI-t helyileg futtatja, akkor egy olyan fiókkal is futtatnia kell az Azure-t, és be kell jelentkeznie az Azure-ba, amely rendelkezik `az login` a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)

Lekérte egy hálózati adapter hatályos útvonalait [az az network nic show-effective-route-table használatával.](/cli/azure/network/nic#az_network_nic_show_effective_route_table) Az alábbi példa lekérte egy *myVMNic1* nevű hálózati adapter hatályos útvonalait, amely egy *myResourceGroup nevű erőforráscsoportban található:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

A kimenetben visszaadott információkért tekintse meg az [Útválasztás áttekintése témakört.](virtual-networks-udr-overview.md) A kimenet csak akkor lesz visszaadva, ha a virtuális gép futó állapotban van. Ha több hálózati adapter van csatlakoztatva a virtuális géphez, áttekintheti az egyes hálózati adapterek hatályos útvonalait. Mivel az egyes hálózati adapterek eltérő alhálózatban is lehet, minden hálózati adapter különböző hatékony útvonalakkal is rendelkezik. Ha továbbra is kommunikációs problémákat lát, tekintse meg a további [diagnosztika és](#additional-diagnosis) [szempontokat.](#considerations)

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatolva van, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati adapterhez visszaadják az adatokat:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Probléma megoldása

Az útválasztási problémák megoldása általában a következőkből áll:

- Egyéni útvonal hozzáadása az Azure egyik alapértelmezett útvonalának felülbírálására. Megtudhatja, [hogyan adhat hozzá egyéni útvonalat.](manage-route-table.md#create-a-route)
- Módosíthat vagy eltávolíthat egy egyéni útvonalat, amely a nem tervezett helyre való útválasztást okozhatja. Megtudhatja, [hogyan módosíthatja](manage-route-table.md#change-a-route) vagy [törölheti az](manage-route-table.md#delete-a-route) egyéni útvonalakat.
- Annak biztosítása, hogy a megadott egyéni útvonalakat tartalmazó útvonaltábla ahhoz az alhálózathoz van társítva, amely a hálózati adaptert tartalmazza. Megtudhatja, hogyan [társítható útválasztási táblázat egy alhálózathoz.](manage-route-table.md#associate-a-route-table-to-a-subnet)
- Annak biztosítása, hogy az Olyan eszközök, mint az Azure VPN Gateway vagy az üzembe helyezett hálózati virtuális berendezések működőképesek. Az Azure [VPN Gateway Network Watcher](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VPN-átjáróval kapcsolatos problémák megállapításához használja a virtuális hálózat VPN-diagnosztikai funkcióját.

Ha továbbra is kommunikációs problémákat tapasztal, tekintse meg a [megfontolandó](#considerations) szempontokat és további diagnosztikát.

## <a name="considerations"></a>Megfontolandó szempontok

Kommunikációs problémák elhárításakor vegye figyelembe a következő pontokat:

- Az útválasztás a megadott útvonalak, a Border Gateway Protocol (BGP) és a rendszerútvonalak közötti leghosszabb előtag-egyezésen (LPM) alapul. Ha egynél több útvonal ugyanazokkal az LPM-egyezéssel van megjelölve, akkor az útvonal a forrásuk alapján lesz kiválasztva az Útválasztás áttekintése [témakörben felsorolt sorrendben.](virtual-networks-udr-overview.md#how-azure-selects-a-route) Az hatályos útvonalak esetében az összes elérhető útvonal alapján csak az LPM-egyezésnek megfelelő, hatályos útvonalak érhetők el. Az útvonalak hálózati adapterre vonatkozó kiértékelésének köszönhetően sokkal könnyebben hárítható el olyan útvonalak hibaelhárítása, amelyek hatással lehetnek a virtuális gép kommunikációjára.
- Ha egyéni útvonalakat adott meg egy hálózati virtuális  berendezéshez (NVA), és a következő ugrás típusa virtuális berendezés, győződjön meg arról, hogy az IP-továbbítás engedélyezve van a forgalmat fogadó NVA-n, vagy a csomagok el vannak dobva. További információ az IP-továbbítás hálózati adapterhez [való engedélyezéséről.](virtual-network-network-interface.md#enable-or-disable-ip-forwarding) Emellett az NVA-n belüli operációs rendszernek vagy alkalmazásnak is képesnek kell lennie a hálózati forgalom továbbításra, és konfigurálva kell lennie erre.
- Ha létrehozott egy útvonalat a 0.0.0.0/0 útvonalra, az összes kimenő internetes forgalom a megadott következő ugráshoz lesz irányítva, például egy NVA-hoz vagy VPN-átjáróhoz. Az ilyen útvonalakat gyakran kényszerített bújtatásnak is nevezik. Előfordulhat, hogy az internetről a virtuális gépre rdp vagy SSH protokollt használó távoli kapcsolatok nem működnek ezzel az útvonalsal attól függően, hogy a következő ugrás hogyan kezeli a forgalmat. A kényszerített bújtatás engedélyezhető:
    - Hely–hely VPN használata esetén hozzon létre egy következő ugrás típusú útvonalat az *VPN Gateway.* További információ a [kényszerített bújtatás konfigurálásával kapcsolatban.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - Ha egy 0.0.0.0/0 (alapértelmezett útvonal) BGP-en keresztül van meghirdetve egy virtuális hálózati átjárón keresztül, egy hely–hely VPN vagy ExpressRoute-kapcsolatcsoport használata esetén. További információ a BGP-nek a [hely–hely VPN-hez](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [ExpressRoute-hoz való használatával kapcsolatban.](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)
- Ahhoz, hogy a virtuális hálózatok közötti társviszony forgalma  megfelelően működjön, léteznie kell egy virtuális társhálózat-létesítés következő ugrás típusú rendszerútvonalnak a virtuális társhálózat előtagtartományához. Ha ez az útvonal nem létezik, és a virtuális hálózatok közötti társviszony-létesítés csatolása **Csatlakoztatva:**
    - Várjon néhány másodpercet, és próbálja újra. Ha ez egy újonnan létrehozott társviszony-létesítési hivatkozás, az útvonalak propagálása néha hosszabb időt vesz igénybe az alhálózat összes hálózati adapterére. További információ a virtuális hálózatok közötti társviszony-létesítésről: Virtuális hálózatok közötti társviszony-létesítés [áttekintése](virtual-network-peering-overview.md) és virtuális hálózatok közötti [társviszony kezelése.](virtual-network-manage-peering.md)
    - A hálózati biztonsági csoport szabályai hatással lehetnek a kommunikációra. További információ: Virtuális gép [hálózatiforgalom-szűrési problémájának diagnosztizálása.](diagnose-network-traffic-filter-problem.md)
- Bár az Azure minden Azure hálózati adapterhez hozzárendel alapértelmezett útvonalakat, ha több hálózati adapter van csatlakoztatva a virtuális géphez, csak az elsődleges hálózati adapterhez van hozzárendelve egy alapértelmezett útvonal (0.0.0.0/0) vagy egy átjáró a virtuális gép operációs rendszerében. Megtudhatja, hogyan hozhat létre alapértelmezett útvonalat a [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy Linux rendszerű virtuális gépekhez csatolt másodlagos hálózati [adapterek](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) számára. További információ az [elsődleges és a másodlagos hálózati adapterről.](virtual-network-network-interface-vm.md#constraints)

## <a name="additional-diagnosis"></a>További diagnosztika

* Egy gyorsteszt futtatásához, amely meghatározza a helyre szánt forgalom [](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) következő ugrási típusát, használja az Azure-beli virtuális gép Következő ugrás Network Watcher. A következő ugrásból megtudhatja, hogy mi a következő ugrás típusa a megadott helyre szánt forgalomhoz.
* Ha nincsenek olyan útvonalak, amelyek miatt a virtuális gép hálózati kommunikációja meghiúsul, a problémát a virtuális gép operációs rendszerében futó tűzfalszoftver okozhatja
* Ha egy [](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VPN-átjárón vagy NVA-n keresztül kényszeríti a forgalom bújtatását egy helyszíni eszközre, előfordulhat, hogy nem tud csatlakozni egy virtuális géphez az internetről, attól függően, hogy hogyan konfigurálta az eszközök útválasztását. Győződjön meg arról, hogy az eszközhöz konfigurált útválasztás a virtuális gép nyilvános vagy magánhálózati IP-címére fogja a forgalmat.
* A kapcsolat [hibaelhárítási](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkciójának Network Watcher a kimenő kommunikációs problémák útválasztásának, szűrésének és az operációs rendszeren való használatának okait.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az útvonaltáblák és útvonalak összes feladatát, tulajdonságát [és beállítását.](manage-route-table.md)
- Megismerheti az összes [következő ugrási típust, rendszerútvonalat, és](virtual-networks-udr-overview.md)hogy az Azure hogyan választ útvonalat.
