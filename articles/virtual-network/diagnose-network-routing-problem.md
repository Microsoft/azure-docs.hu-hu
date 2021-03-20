---
title: Azure-beli virtuális gépek útválasztási problémáinak diagnosztizálása | Microsoft Docs
description: A virtuális gép útválasztási problémáinak diagnosztizálásához tekintse meg a virtuális gép érvényes útvonalait.
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
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84705049"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Virtuális gép útválasztási problémáinak diagnosztizálása

Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja az útválasztási problémákat úgy, hogy megtekinti a virtuális gép (VM) hálózati adapteréhez érvényes útvonalakat. Az Azure több alapértelmezett útvonalat hoz létre minden egyes virtuális hálózati alhálózathoz. Az Azure alapértelmezett útvonalait az útválasztási táblázatban megadott útvonalak definiálásával felülbírálhatja, majd az útválasztási táblázatot társíthatja egy alhálózathoz. A létrehozott útvonalak, az Azure alapértelmezett útvonalak és a helyszíni hálózatról az Azure VPN Gateway használatával propagált útvonalak kombinációja (ha a virtuális hálózat a helyszíni hálózathoz csatlakozik) a Border Gateway Protocol (BGP) használatával történik, az egy alhálózat összes hálózati adapterének érvényes útvonala. Ha nem ismeri a virtuális hálózatot, a hálózati adaptert vagy az útválasztási fogalmakat, tekintse meg a [virtuális hálózat áttekintése](virtual-networks-overview.md), a [hálózati adapter](virtual-network-network-interface.md)és az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört.

## <a name="scenario"></a>Eset

Megpróbál csatlakozni egy virtuális géphez, de a kapcsolat meghiúsul. Annak megállapításához, hogy miért nem tud csatlakozni a virtuális géphez, az Azure [Portal](#diagnose-using-azure-portal), a [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli)használatával megtekintheti a hálózati adapter érvényes útvonalait.

Az alábbi lépések feltételezik, hogy rendelkezik egy meglévő virtuális géppel a hatályos útvonalak megtekintéséhez. Ha nem rendelkezik meglévő virtuális géppel, először telepítsen egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet a cikkben ismertetett feladatok végrehajtásához. A jelen cikkben szereplő példák egy *myVM* nevű virtuális gépre vonatkoznak egy *myVMNic1* nevű hálózati adapterrel. A virtuális gép és a hálózati adapter egy *myResourceGroup* nevű erőforráscsoport, és az *USA keleti* régiójában található. Szükség szerint módosítsa a lépésekben szereplő értékeket arra a virtuális gépre, amelyre a problémát diagnosztizálja.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálás a Azure Portal használatával

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com) egy olyan Azure-fiókkal, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).
2. A Azure Portal tetején adja meg a futó állapotú virtuális gép nevét a keresőmezőbe. Ha a virtuális gép neve megjelenik a keresési eredmények között, válassza ki.
3. A bal oldali **Beállítások** területen válassza a **hálózatkezelés** lehetőséget, majd a név kiválasztásával navigáljon a hálózati adapter erőforrásához.
     ![Hálózati adapterek megtekintése](./media/diagnose-network-routing-problem/view-nics.png)
4. A bal oldalon válassza a **hatályos útvonalak** lehetőséget. A **myVMNic1** nevű hálózati adapter érvényes útvonalait a következő képen tekintheti meg: az ![ érvényes útvonalak megtekintése](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Ha a virtuális géphez több hálózati adapter van csatlakoztatva, a kiválasztásával megtekintheti az összes hálózati adapter érvényes útvonalait. Mivel az egyes hálózati adapterek eltérő alhálózatokban is lehetnek, az egyes hálózati adapterek különböző tényleges útvonalakkal rendelkezhetnek.

    Az előző képen látható példában a felsorolt útvonalak az alapértelmezett útvonalak, amelyeket az Azure az egyes alhálózatokhoz hoz létre. A lista legalább ezen útvonalakat tartalmazza, de további útvonalak is lehetnek, attól függően, hogy milyen képességekkel rendelkezett a virtuális hálózatban, például egy másik virtuális hálózattal vagy a helyszíni hálózathoz egy Azure VPN Gateway használatával csatlakoztatva. Ha többet szeretne megtudni az egyes útvonalakról, valamint a hálózati adapterhez kapcsolódó egyéb útvonalakról, tekintse meg a [virtuális hálózati forgalom útválasztását](virtual-networks-udr-overview.md)ismertető témakört. Ha a lista nagy számú útvonalat tartalmaz, akkor könnyebben kiválaszthatja a **Letöltés** lehetőséget, hogy egy. csv-fájlt töltsön le az útvonalak listájával.

Bár az előző lépések során a virtuális gépen megtekintett tényleges útvonalakat, a tényleges útvonalakat a következő módon is megtekintheti:
- **Egyéni hálózati adapter**: megtudhatja, hogyan [tekintheti meg a hálózati adaptereket](virtual-network-network-interface.md#view-network-interface-settings).
- **Egyéni útválasztási táblázat**: útmutató az [útválasztási táblázat megtekintéséhez](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosztizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0 vagy újabb verzióra. Futtassa a parancsot a `Get-Module -ListAvailable Az` számítógépen, és keresse meg a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor is futtatnia kell, `Connect-AzAccount` hogy az Azure-ba jelentkezzen be egy olyan fiókkal, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).

Szerezze be a [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)hálózati adapter érvényes útvonalait. A következő példa lekéri a *myVMNic1* nevű hálózati adapter érvényes útvonalait, amely egy *myResourceGroup* nevű erőforráscsoport:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

A kimenetben visszaadott információk megismeréséhez tekintse meg az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört. A rendszer csak akkor adja vissza a kimenetet, ha a virtuális gép futó állapotban van. Ha a virtuális géphez több hálózati adapter van csatlakoztatva, tekintse át az egyes hálózati adapterek érvényes útvonalait. Mivel az egyes hálózati adapterek eltérő alhálózatokban is lehetnek, az egyes hálózati adapterek különböző tényleges útvonalakkal rendelkezhetnek. Ha továbbra is kommunikációs problémája van, tekintse meg a [további diagnosztikai](#additional-diagnosis) és [szempontokat](#considerations).

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatolt összes hálózati adapter azonosítóit adják vissza:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Az alábbi példához hasonló kimenetet kap:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Az előző kimenetben a hálózati adapter neve *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálás az Azure CLI használatával

Az alábbi parancsokat futtathatja a  [Azure Cloud Shell](https://shell.azure.com/bash), vagy futtathatja a CLI-t a számítógépről is. Ehhez a cikkhez az Azure CLI 2.0.32 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, a `az login` [szükséges engedélyekkel](virtual-network-network-interface.md#permissions)rendelkező fiókkal is futtatnia kell az Azure-ba, és be kell jelentkeznie.

Szerezze be a hálózati adapter érvényes útvonalait az [az Network NIC show-effektív-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)paranccsal. A következő példa egy *myVMNic1* nevű hálózati adapter érvényes útvonalait olvassa be, amely egy *myResourceGroup* nevű erőforráscsoport:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

A kimenetben visszaadott információk megismeréséhez tekintse meg az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört. A rendszer csak akkor adja vissza a kimenetet, ha a virtuális gép futó állapotban van. Ha a virtuális géphez több hálózati adapter van csatlakoztatva, tekintse át az egyes hálózati adapterek érvényes útvonalait. Mivel az egyes hálózati adapterek eltérő alhálózatokban is lehetnek, az egyes hálózati adapterek különböző tényleges útvonalakkal rendelkezhetnek. Ha továbbra is kommunikációs problémája van, tekintse meg a [további diagnosztikai](#additional-diagnosis) és [szempontokat](#considerations).

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatolt összes hálózati adapter azonosítóit adják vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Probléma megoldása

Az útválasztási problémák megoldása általában a következőkből áll:

- Egyéni útvonal hozzáadása az Azure egyik alapértelmezett útvonalának felülbírálásához. Útmutató [Egyéni útvonal hozzáadásához](manage-route-table.md#create-a-route).
- Olyan egyéni útvonal módosítása vagy eltávolítása, amely nem kívánt helyre irányíthatja az útválasztást. Megtudhatja, hogyan [módosíthatja](manage-route-table.md#change-a-route) vagy [törölheti](manage-route-table.md#delete-a-route) az egyéni útvonalakat.
- Annak biztosítása, hogy a megadott egyéni útvonalakat tartalmazó útválasztási tábla a hálózati adapterhez tartozó alhálózathoz legyen társítva. Megtudhatja, hogyan [rendelhet hozzá útválasztási táblázatot egy alhálózathoz](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Gondoskodhat arról, hogy az eszközök, például az Azure VPN Gateway vagy a telepített hálózati virtuális berendezések működőképesek legyenek. Az Azure VPN-átjáróval kapcsolatos esetleges problémák meghatározásához használja a Network Watcher [VPN-diagnosztika](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkcióját.

Ha továbbra is kommunikációs problémák léptek fel, tekintse meg a [szempontokat](#considerations) és a további diagnózist.

## <a name="considerations"></a>Megfontolandó szempontok

A kommunikációs problémák elhárításakor vegye figyelembe a következő szempontokat:

- Az Útválasztás a megadott útvonalak közül a leghosszabb előtagon (LPM) alapul, a Border Gateway Protocol (BGP) és a System Routes között. Ha egynél több útvonal van ugyanazzal a LPM-egyeztetéssel, akkor az útvonal az [Útválasztás áttekintése](virtual-networks-udr-overview.md#how-azure-selects-a-route)részben megadott sorrendben van kiválasztva. A tényleges útvonalakkal csak az összes elérhető útvonal alapján láthatja a LPM-egyeztetést eredményező tényleges útvonalakat. Megtudhatja, hogyan értékeli ki az útvonalakat a hálózati adapterek számára, így sokkal könnyebben elháríthatja azokat a konkrét útvonalakat, amelyek hatással lehetnek a virtuális gépről folytatott kommunikációra.
- Ha egyéni útvonalakat adott meg egy hálózati virtuális berendezés (NVA) számára, és a *virtuális berendezést* a következő ugrás típusaként adja meg, akkor győződjön meg arról, hogy az IP-továbbítás engedélyezve van a FORGALMAT fogadó NVA, vagy a csomagok el lesznek dobva. További információ a [hálózati adapterek IP-továbbításának engedélyezéséről](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Emellett a NVA belüli operációs rendszernek vagy alkalmazásnak is képesnek kell lennie továbbítani a hálózati forgalmat, és úgy kell konfigurálni.
- Ha a 0.0.0.0/0 útvonalhoz hozott létre útvonalat, az összes kimenő internetes forgalom a megadott következő ugráshoz lesz irányítva, például egy NVA vagy egy VPN-átjáróhoz. Az ilyen útvonalak létrehozását gyakran kényszerített bújtatásnak nevezzük. Előfordulhat, hogy az internetről a virtuális gépre irányuló RDP-vagy SSH-protokollt használó távoli kapcsolatok nem működnek ezzel az útvonallal, attól függően, hogy a következő ugrás hogyan kezeli a forgalmat. A kényszerített bújtatás engedélyezve lehet:
    - Helyek közötti VPN használata esetén a következő ugrási típussal rendelkező útvonal létrehozásával *VPN Gateway*. További információ a [kényszerített bújtatás konfigurálásáról](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Ha a 0.0.0.0/0 (alapértelmezett útvonal) egy virtuális hálózati átjárón keresztül, helyek közötti VPN-vagy ExpressRoute-áramkör használatával hirdeti meg a BGP-t. További információ a BGP és a [helyek közötti VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -vagy [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)történő használatáról.
- A virtuális hálózati összevonási forgalom megfelelő működéséhez a *VNet* -társítás következő ugrási típusával rendelkező rendszerútvonalnak léteznie kell a társ virtuális hálózat előtag-tartományához. Ha egy ilyen útvonal nem létezik, és a virtuális hálózati társítási hivatkozás **csatlakozik**:
    - Várjon néhány másodpercet, és próbálkozzon újra. Ha ez egy újonnan létesített társ-összevonási hivatkozás, időnként az alhálózat összes hálózati adaptere számára tovább tart az útvonalak propagálása. A virtuális hálózatokkal kapcsolatos további tudnivalókért lásd: virtuális hálózati társítások [áttekintése](virtual-network-peering-overview.md) és a [virtuális hálózatok kezelése](virtual-network-manage-peering.md).
    - A hálózati biztonsági csoportra vonatkozó szabályok befolyásolhatják a kommunikációt. További információ: [virtuális gép hálózati forgalmának szűrése – probléma](diagnose-network-traffic-filter-problem.md).
- Bár az Azure alapértelmezett útvonalakat rendel az egyes Azure-hálózati adapterekhez, ha a virtuális géphez több hálózati adapter van csatlakoztatva, csak az elsődleges hálózati adapter lesz hozzárendelve alapértelmezett útvonal (0.0.0.0/0) vagy átjáró a virtuális gép operációs rendszerén belül. Megtudhatja, hogyan hozhat létre alapértelmezett útvonalat a Windows vagy [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [rendszerű](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) virtuális gépekhez csatlakoztatott másodlagos hálózati adapterekhez. További információ az [elsődleges és a másodlagos hálózati adapterekről](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>További diagnosztika

* Ha egy gyors tesztet szeretne futtatni, hogy meghatározza a következő ugrás típusát az adott helyre irányuló forgalomhoz, használja az Azure Network Watcher [következő ugrási](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkcióját. A következő ugrás azt jelzi, hogy a következő ugrás típusa a megadott helyre irányuló forgalomra vonatkozik.
* Ha nincs olyan útvonal, amely miatt a virtuális gép hálózati kommunikációja meghiúsul, a probléma oka lehet a virtuális gép operációs rendszerén futó tűzfal szoftvere.
* Ha [kényszeríti](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a forgalmat egy helyszíni eszközre egy VPN-átjárón vagy NVA keresztül, akkor előfordulhat, hogy nem tud csatlakozni az internetről a virtuális géphez, attól függően, hogy hogyan konfigurálta az útválasztást az eszközökhöz. Győződjön meg arról, hogy az eszközhöz konfigurált Útválasztás a virtuális gép nyilvános vagy magánhálózati IP-címére irányítja a forgalmat.
* A Network Watcher [kapcsolódási hibaelhárítási](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkciója segítségével meghatározhatja a kimenő kommunikációs problémák útválasztását, szűrését és operációs rendszeren belüli okait.

## <a name="next-steps"></a>Következő lépések

- Az [útválasztási táblák és útvonalak](manage-route-table.md)összes feladatának, tulajdonságának és beállításának megismerése.
- Ismerje meg az összes [következő ugrási típust, a rendszer útvonalait, valamint azt, hogy az Azure hogyan válasszon ki egy útvonalat](virtual-networks-udr-overview.md).
