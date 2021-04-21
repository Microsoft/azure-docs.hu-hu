---
title: Azure-beli útválasztási táblázat létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hol találhat információt a virtuális hálózati forgalom útválasztásról, és hogyan hozhat létre, módosíthat vagy törölhet útválasztási táblázatokat.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: cdf702abb10b7330a4ca0f5478751df4bce3d7f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783376"
---
# <a name="create-change-or-delete-a-route-table"></a>Útválasztási táblázat létrehozása, módosítása vagy törlése

Az Azure automatikusan elirányja a forgalmat az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között. Ha módosítani szeretné az Azure alapértelmezett útválasztását, hozzon létre egy útvonaltáblát. Ha még csak most ismerkedik a virtuális hálózatok útválasztási folyamattal, a virtuális hálózatok forgalmának útválasztását ismertető oktatóanyagban vagy egy oktatóanyag [](virtual-networks-udr-overview.md) [elvégzésével ismerkedhet meg vele.](tutorial-create-route-table-portal.md)

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik előfizetéssel, állítson be egy aktív előfizetéssel egy Azure-fiókot. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ezután a cikk bármely szakaszában el kell végrehajtania az alábbi feladatok valamelyikét, mielőtt elkezdi a lépéseket:

- **Portálfelhasználók:** Jelentkezzen [](https://portal.azure.com) be Azure Portal azure-fiókjával.

- **PowerShell-felhasználók:** Futtassa a parancsokat a Azure Cloud Shell, [vagy](https://shell.azure.com/powershell)futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell lapon keresse meg a  Környezet kiválasztása legördülő listát, majd válassza a **PowerShellt,** ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az `Connect-AzAccount` Azure-ral való kapcsolat létrehozásához futtassa a (futtatás) futtatását is.

- **Azure parancssori felület (CLI)** felhasználói: Futtassa [](https://shell.azure.com/bash)a Azure Cloud Shell parancsokat, vagy futtassa a parancssori felületet a számítógépről. Ha az Azure CLI-t helyileg futtatja, használja az Azure CLI 2.0.31-es vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Az `az login` Azure-ral való kapcsolat létrehozásához futtassa a (futtatás) futtatását is.

Ahhoz a fiókhoz, amelybe bejelentkezik, vagy [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) amelyről csatlakozik az Azure-hoz, [hozzá](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kell rendelni a Hálózati közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz, amely hozzá van rendelve a Permissions (Engedélyek) listában felsorolt megfelelő [műveletekhez.](#permissions)

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az Azure-helyen és -előfizetésben létrehozható útvonaltáblák száma korlátozva van. Részletekért lásd: [Hálózati korlátok – Azure Resource Manager.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be a Következőt: *Útvonaltábla.* Amikor **megjelenik az** Útválasztási táblázat a keresési eredmények között, válassza ki.

1. Az **Útvonaltábla lapon** válassza a Létrehozás **lehetőséget.**

1. Az **Útvonaltábla létrehozása párbeszédpanelen:**

    1. Adja meg az **útvonaltábla** Nevét.
    1. Válassza ki az **Előfizetést**.
    1. Válasszon ki egy meglévő **erőforráscsoportot,** vagy válassza az **Új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.
    1. Válasszon egy **helyet.**
    1. Ha az útválasztási táblázatot egy olyan virtuális hálózat alhálózatához szeretné társítani, amely VPN-átjárón keresztül csatlakozik a helyszíni hálózathoz, és nem szeretné propagálni a helyszíni útvonalakat az alhálózat hálózati adapterei felé, állítsa a **Virtuális** hálózati átjáró útvonal-propagálása beállítását Letiltva **beállításra.**

1. Az **új útvonaltábla** létrehozásához válassza a Létrehozás lehetőséget.

### <a name="create-route-table---commands"></a>Útvonaltábla létrehozása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Útvonaltáblák megtekintése

A virtuális hálózat [Azure Portal](https://portal.azure.com) a virtuális hálózat kezeléséhez ugrás a virtuális gépre. Keresse meg és válassza az **Útvonaltáblák lehetőséget.** Megjelenik az előfizetésben található útvonaltáblák listája.

### <a name="view-route-table---commands"></a>Útvonaltábla megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table list](/cli/azure/network/route-table#az_network_route_table_list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Útvonaltábla részleteinek megtekintése

1. A virtuális hálózat [Azure Portal](https://portal.azure.com) a virtuális hálózat kezeléséhez ugrás a virtuális gépre. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki azt az útvonaltáblát, amely adatait meg szeretné tekinteni.

1. Az útválasztási táblázat oldalán, a  **Beállítások** alatt tekintse  meg az útvonaltáblában található Útvonalakat vagy az Alhálózatokat, amelyekhez az útvonaltábla társítva van.

A gyakori Azure-beállításokkal kapcsolatos további információkért tekintse meg a következő információkat:

- [Tevékenységnapló](../azure-monitor/essentials/platform-logs-overview.md)
- [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
- [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-szkript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Útvonaltábla részleteinek megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table show](/cli/azure/network/route-table#az_network_route_table_show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Útvonaltábla módosítása

1. A virtuális hálózat [Azure Portal](https://portal.azure.com) a virtuális hálózat kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki a módosítani kívánt útválasztási táblázatot.

A leggyakoribb módosítás [](#create-a-route) az útvonalak [hozzáadása,](#delete-a-route) az útvonalak [eltávolítása,](#associate-a-route-table-to-a-subnet) az [](#dissociate-a-route-table-from-a-subnet) útvonaltáblák alhálózatokhoz való társítása vagy az útvonaltáblák alhálózatok társításának eltávolítása.

### <a name="change-a-route-table---commands"></a>Útvonaltábla módosítása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table update](/cli/azure/network/route-table#az_network_route_table_update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Igény szerint hozzárendelhet egy útválasztási táblázatot egy alhálózathoz. Az útvonaltáblák nulla vagy több alhálózathoz társíthatóak. Mivel az útvonaltáblák nincsenek virtuális hálózatokhoz társítva, minden alhálózathoz hozzá kell társítania egy útválasztási táblázatot, amelyhez hozzá szeretné társítani az útválasztási táblázatot. Az Azure átirányítja az alhálózatot elhagyó összes forgalmat az útvonaltáblákban [](virtual-networks-udr-overview.md#default)létrehozott útvonalak, az alapértelmezett útvonalak és a helyszíni hálózatról propagált útvonalak alapján, ha a virtuális hálózat egy Azure-beli virtuális hálózati átjáróhoz (ExpressRoute vagy VPN) csatlakozik. Útvonaltáblát csak olyan virtuális hálózatok alhálózatához társíthat, amelyek ugyanazon az Azure-helyen és előfizetésben vannak, mint az útvonaltábla.

1. A virtuális hálózat [Azure Portal](https://portal.azure.com) a virtuális hálózat kezeléséhez. Keresse meg és válassza a **Virtuális hálózatok lehetőséget.**

1. A virtuális hálózat listában válassza ki azt a virtuális hálózatot, amely azt az alhálózatot tartalmazza, amelyhez útválasztási táblázatot szeretne társítani.

1. A virtuális hálózat menüsávjában válassza az **Alhálózatok lehetőséget.**

1. Válassza ki azt az alhálózatot, amelyhez társítani szeretné az útválasztási táblázatot.

1. Az **Útválasztási táblázatban** válassza ki az alhálózathoz társítani kívánt útválasztási táblázatot.

1. Kattintson a **Mentés** gombra.

Ha a virtuális hálózat egy Azure VPN-átjáróhoz csatlakozik, [](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) ne társítson útválasztási táblázatot ahhoz az átjáró-alhálózathoz, amely *0.0.0.0/0* céllal rendelkező útvonalat tartalmaz. Ellenkező esetben előfordulhat, hogy az átjárószolgáltatás nem működik megfelelően. További információ a *0.0.0.0/0* útvonalon való használatával kapcsolatban: Virtuális hálózati [forgalom útválasztása.](virtual-networks-udr-overview.md#default-route)

### <a name="associate-a-route-table---commands"></a>Útvonaltábla társítása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Útvonaltábla társításának társításának társítása alhálózatról

Amikor egy alhálózatról nem társít útválasztási táblázatot, az Azure az alapértelmezett útvonalak alapján irányíthatja a [forgalmat.](virtual-networks-udr-overview.md#default)

1. A virtuális hálózat [Azure Portal](https://portal.azure.com) a virtuális hálózat kezeléséhez ugrás a virtuális gépre. Keresse meg és válassza a **Virtuális hálózatok lehetőséget.**

1. A virtuális hálózat listában válassza ki azt a virtuális hálózatot, amely azt az alhálózatot tartalmazza, amelyről le szeretné választani az útválasztási táblázatot.

1. A virtuális hálózat menüsávjában válassza az **Alhálózatok lehetőséget.**

1. Válassza ki azt az alhálózatot, amelyről le szeretné választani az útválasztási táblázatot.

1. Az **Útválasztási táblázatban válassza** a **Nincs lehetőséget.**

1. Kattintson a **Mentés** gombra.

### <a name="dissociate-a-route-table---commands"></a>Útvonaltábla társításának társításának társítása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Útvonaltábla törlése

Egy alhálózathoz társított útválasztási táblázatot nem törölhet. [Egy útvonaltábla társításának](#dissociate-a-route-table-from-a-subnet) törlése az összes alhálózatról a törlés megkísérlését megelőzően.

1. Az [útvonaltáblák Azure Portal](https://portal.azure.com) az útvonaltáblák kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki a törölni kívánt útvonaltáblát.

1. Válassza **a Törlés** lehetőséget, majd a megerősítési párbeszédpanelen válassza az Igen lehetőséget. 

### <a name="delete-a-route-table---commands"></a>Útvonaltábla törlése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table delete](/cli/azure/network/route-table#az_network_route_table_delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Útvonal létrehozása

Az útvonaltáblák által létrehozható útvonalak száma Azure-helyenként és előfizetésenként korlátozva van. Részletekért lásd: [Hálózati korlátok – Azure Resource Manager.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Az [útvonaltáblák Azure Portal](https://portal.azure.com) az útvonaltáblák kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki azt az útvonaltáblát, amelybe az útvonalat hozzá szeretné adni.

1. Az útvonaltábla menüsávján válassza az **Útvonalak Hozzáadása**  >  **lehetőséget.**

1. Adjon meg egy **egyedi útvonalnevet** az útvonaltáblán belül.

1. Adja meg **a címelőtagot** a Classless Inter-Domain Routing (CIDR) (Útválasztás (CIDR) szövegben, amelybe a forgalmat át szeretné irányítva. Az előtag nem duplikálható egynél több útvonalon az útvonaltáblában, bár az előtag egy másik előtagon belül is lehet. Ha például a *10.0.0.0/16* címet egy útvonal előtagjaként definiálta, akkor is definiálhat egy másik útvonalat a *10.0.0.0/22* címelőtaggal. Az Azure kiválasztja a forgalom útvonalát a leghosszabb előtag-egyezés alapján. További információ: [How Azure selects a route (Útvonal kiválasztása az Azure-ban).](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Válasszon egy **következő ugrási típust.** A következő ugrási típusokkal kapcsolatos további információkért lásd: [Virtuális hálózati forgalom útválasztása.](virtual-networks-udr-overview.md)

1. Ha a Következő ugrás **típusú** virtuális berendezést **választotta,** adjon meg egy IP-címet a Következő ugrás **címeként.**

1. Válassza az **OK** lehetőséget.

### <a name="create-a-route---commands"></a>Útvonal létrehozása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Útvonalak megtekintése

Az útvonaltáblák nulla vagy több útvonalat tartalmaznak. Az útvonalak megtekintésekor felsorolt információkkal kapcsolatos további információkért lásd: [Virtuális hálózati forgalom útválasztása.](virtual-networks-udr-overview.md)

1. Az [útvonaltáblák Azure Portal](https://portal.azure.com) az útvonaltáblák kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki azt az útvonaltáblát, amely számára meg szeretné tekinteni az útvonalakat.

1. Az útvonaltábla menüsávján válassza az **Útvonalak elemet** az útvonalak listájának a listájának a listájához.

### <a name="view-routes---commands"></a>Útvonalak megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route list](/cli/azure/network/route-table/route#az_network_route_table_route_list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. Az [útvonaltáblák Azure Portal](https://portal.azure.com) az útvonaltáblák kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki azt az útvonaltáblát, amely tartalmazza azt az útvonalat, amely adatait meg szeretné tekinteni.

1. Az útvonaltábla menüsávján válassza az **Útvonalak elemet** az útvonalak listájának a listájának a listájához.

1. Válassza ki azt az útvonalat, amelyről meg szeretné tekinteni a részleteket.

### <a name="view-details-of-a-route---commands"></a>Útvonal részleteinek megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route show](/cli/azure/network/route-table/route#az_network_route_table_route_show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Útvonal módosítása

1. Az [útvonaltáblák Azure Portal](https://portal.azure.com) az útvonaltáblák kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki a módosítani kívánt útvonalat tartalmazó útválasztási táblázatot.

1. Az útvonaltábla menüsávján válassza az **Útvonalak elemet** az útvonalak listájának a listájának a listájához.

1. Válassza ki a módosítani kívánt útvonalat.

1. Módosítsa a meglévő beállításokat az új beállításokra, majd válassza a **Mentés lehetőséget.**

### <a name="change-a-route---commands"></a>Útvonal módosítása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route update](/cli/azure/network/route-table/route#az_network_route_table_route_update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Útvonal törlése

1. Az [útvonaltáblák Azure Portal](https://portal.azure.com) az útvonaltáblák kezeléséhez. Keresse meg és válassza az **Útvonaltáblák lehetőséget.**

1. Az útvonaltábla listájában válassza ki a törölni kívánt útvonalat tartalmazó útvonaltáblát.

1. Az útvonaltábla menüsávján válassza az **Útvonalak elemet** az útvonalak listájának a listájának a listájához.

1. Válassza ki a törölni kívánt útvonalat.

1. Válassza **a Törlés** lehetőséget, majd a megerősítési párbeszédpanelen válassza az Igen lehetőséget. 

### <a name="delete-a-route---commands"></a>Útvonal törlése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route delete](/cli/azure/network/route-table/route#az_network_route_table_route_delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Hatályos útvonalak megtekintése

Az egyes virtuális gépekhez csatolt hálózati adapterek érvényes útvonalai a létrehozott útvonaltáblák, az Azure alapértelmezett útvonalai és a helyszíni hálózatokról az Border Gateway Protocol-n (BGP) keresztül egy Azure-beli virtuális hálózati átjárón keresztül propagált útvonalak kombinációja. A hálózati adapterek hatékony útvonalának ismertetése hasznos lehet az útválasztási problémák elhárításakor. Megtekintheti a futó virtuális géphez csatolt hálózati adapterek hatályos útvonalait.

1. A virtuális gépek [kezeléséhez Azure Portal](https://portal.azure.com) a virtuális gépeket. Keresse meg és válassza a **Virtuális gépek lehetőséget.**

1. A virtuális gépek listájában válassza ki azt a virtuális gépet, amely számára meg szeretné tekinteni az hatályos útvonalakat.

1. A virtuális gép menüsávjában válassza a **Hálózat lehetőséget.**

1. Válassza ki a hálózati adapter nevét.

1. A hálózati adapter menüsávjában válassza az Hatályos **útvonalak lehetőséget.**

1. Tekintse át az hatályos útvonalak listáját, és ellenőrizze, hogy a megfelelő útvonal létezik-e arra a helyre, ahová a forgalmat át szeretné irányítani. További információ a következő ugrási típusokról, amelyek a Virtuális hálózati forgalom útválasztása [listában megjelenik.](virtual-networks-udr-overview.md)

### <a name="view-effective-routes---commands"></a>Hatályos útvonalak megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Útválasztás ellenőrzése két végpont között

Meghatározhatja a következő ugrás típusát egy virtuális gép és egy másik Azure-erőforrás, egy helyszíni erőforrás vagy egy internetes erőforrás IP-címe között. Az Azure útválasztásának meghatározása hasznos lehet az útválasztási problémák elhárítása során. A feladat végrehajtásához egy meglévő Network Watcherrel kell lennie. Ha még nem létezik Network Watcher, hozzon létre egyet a Create a Network Watcher instance (Új hálózati példány [létrehozása) Network Watcher lépéseit követve.](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

1. A hálózati [Azure Portal](https://portal.azure.com) kezeléséhez tekintse meg a hálózati figyelőket. Keresse meg és válassza a **Network Watcher.**

1. A Network Watcher menüsávjában válassza a **Következő ugrás lehetőséget.**

1. A **Network Watcher | Következő ugrás** lap:

    1. Válassza ki **az Előfizetést** és **annak a** forrás virtuális gépnek az Erőforráscsoportját, amelyről ellenőrizni szeretné az útválasztást.

    1. Válassza ki **a virtuális gépet** és **a** virtuális géphez csatolt hálózati adaptert.
    
    1. Adja  meg annak a hálózati adapternek a forrás IP-címét, amelyről ellenőrizni szeretné az útválasztást.

    1. Adja meg azt a **cél IP-címet,** amelybe érvényesíteni szeretné az útválasztást.

1. Válassza **a Következő ugrás lehetőséget.**

Rövid várakozás után az Azure közli a következő ugrás típusát és a forgalmat irányító útvonal azonosítóját. További információ a virtuális hálózati forgalom-útválasztásban visszaadott következő [ugrástípusokról.](virtual-networks-udr-overview.md)

### <a name="validate-routing-between-two-endpoints---commands"></a>Útválasztás ellenőrzése két végpont között – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Engedélyek

Az útvonaltáblák és útvonalak feladatainak végrehajtásához [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) fiókját a Hálózati közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz kell hozzárendelni, amely az alábbi táblázatban felsorolt megfelelő műveleteket rendeli hozzá:

| Művelet                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Útvonaltábla olvasása                                    |
| Microsoft.Network/routeTables/write                             |   Útválasztási táblázat létrehozása vagy frissítése                        |
| Microsoft.Network/routeTables/delete                            |   Útvonaltábla törlése                                  |
| Microsoft.Network/routeTables/join/action                       |   Útválasztási táblázat társítása alhálózattal                   |
| Microsoft.Network/routeTables/routes/read                       |   Útvonal olvasása                                          |
| Microsoft.Network/routeTables/routes/write                      |   Útvonal létrehozása vagy frissítése                              |
| Microsoft.Network/routeTables/routes/delete                     |   Útvonal törlése                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   A hálózati adapter hatályos útválasztási táblázatának lekérte |
| Microsoft.Network/networkWatchers/nextHop/action                |   Lekérte a következő ugrást egy virtuális gépről                           |

## <a name="next-steps"></a>Következő lépések

- Útvonaltábla létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaszk szkriptekkel vagy Azure [Resource Manager sablonokkal](template-samples.md) [](cli-samples.md)
- Virtuális [hálózatokhoz Azure Policy és hozzárendelése](./policy-reference.md)