---
title: Azure-útválasztási táblázat létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hol talál információt a virtuális hálózati forgalom útválasztásáról, valamint arról, hogyan hozhat létre, módosíthat vagy törölhet útválasztási táblázatot.
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
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574131"
---
# <a name="create-change-or-delete-a-route-table"></a>Útválasztási táblázat létrehozása, módosítása vagy törlése

Az Azure automatikusan irányítja a forgalmat az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között. Ha módosítani szeretné az Azure alapértelmezett útválasztását, hozzon létre egy útválasztási táblázatot. Ha még nem ismeri a virtuális hálózatok útválasztását, többet is megtudhat a [virtuális hálózati forgalom útválasztásával](virtual-networks-udr-overview.md) vagy egy [oktatóanyag](tutorial-create-route-table-portal.md)elvégzésével.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik ilyennel, állítson be egy aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ezután hajtsa végre a fenti feladatok egyikét, mielőtt elkezdené a jelen cikk bármely szakaszának lépéseit:

- **Portál felhasználói**: Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók**: futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell böngésző lapon keresse meg a **környezet kiválasztása** legördülő listát, majd válassza a **PowerShell** lehetőséget, ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell modul 1.0.0 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az `Connect-AzAccount` Azure-beli kapcsolatok létrehozásához is futtassa a parancsot.

- **Azure parancssori felület (CLI) felhasználói**: futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy futtassa a CLI-t a számítógépről. Ha helyileg futtatja az Azure CLI-t, használja az Azure CLI-2.0.31 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Az `az login` Azure-beli kapcsolatok létrehozásához is futtassa a parancsot.

A fiókba, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelni a [hálózati közreműködő szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyek](#permissions)között felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az Azure-helyek és-előfizetések által létrehozható útválasztási táblázatok száma korlátozva van. Részletekért lásd: [hálózati korlátok – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. A keresőmezőbe írja be az *útválasztási táblázat* kifejezést. Ha az **útválasztási tábla** megjelenik a keresési eredmények között, válassza ki.

1. Az **útválasztási táblázat** lapon válassza a **Létrehozás** lehetőséget.

1. Az **útválasztási táblázat létrehozása** párbeszédpanelen:

    1. Adja meg az útválasztási táblázat **nevét** .
    1. Válassza ki az **Előfizetést**.
    1. Válasszon ki egy meglévő **erőforráscsoportot** , vagy válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.
    1. Válasszon egy **helyet**.
    1. Ha azt tervezi, hogy az útválasztási táblázatot egy olyan virtuális hálózat alhálózatához rendeli hozzá, amely VPN-átjárón keresztül csatlakozik a helyszíni hálózathoz, és nem szeretné a helyszíni útvonalakat az alhálózat hálózati adapterei között terjeszteni, állítsa le a **virtuális hálózati átjáró útvonal-propagálását** **Letiltva** értékre.

1. Válassza a **Létrehozás** lehetőséget az új útválasztási táblázat létrehozásához.

### <a name="create-route-table---commands"></a>Útválasztási táblázat létrehozása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Útválasztási táblák megtekintése

A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**. Megjelenik az előfizetésben található útválasztási táblák.

### <a name="view-route-table---commands"></a>Útválasztási táblázat megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table List](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Útválasztási táblázat részleteinek megtekintése

1. A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listából válassza ki azt az útválasztási táblázatot, amelyre vonatkozóan meg szeretné tekinteni a részleteit.

1. Az útválasztási táblázat lap **Beállítások** területén tekintse meg az útválasztási táblázatban szereplő **útvonalakat** , illetve azt az **alhálózatot** , amelyhez az útválasztási tábla hozzá van rendelve.

Az általános Azure-beállításokkal kapcsolatos további tudnivalókért tekintse meg a következő információkat:

- [Tevékenységnapló](../azure-monitor/essentials/platform-logs-overview.md)
- [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
- [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-szkript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Útválasztási táblázat részleteinek megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Útválasztási táblázat módosítása

1. A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listában válassza ki a módosítani kívánt útválasztási táblázatot.

A leggyakoribb módosítások az útvonalak [hozzáadása](#create-a-route) , az útvonalak [eltávolítása](#delete-a-route) , az útválasztási táblák [társítása](#associate-a-route-table-to-a-subnet) az alhálózatokhoz [, vagy az](#dissociate-a-route-table-from-a-subnet) útválasztási táblák leválasztása az alhálózatokról.

### <a name="change-a-route-table---commands"></a>Útválasztási táblázat módosítása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Az útválasztási táblázatot igény szerint hozzárendelheti egy alhálózathoz. Az útválasztási táblázatok nulla vagy több alhálózathoz is társíthatók. Mivel az útválasztási táblák nem a virtuális hálózatokhoz vannak társítva, társítania kell egy útválasztási táblázatot minden olyan alhálózathoz, amelyhez hozzá kívánja rendelni az útválasztási táblázatot. Az Azure az alhálózatot elhagyó útvonalakat az útválasztási táblák, az [alapértelmezett útvonalak](virtual-networks-udr-overview.md#default)és a helyszíni hálózatról propagált útvonalak alapján, a virtuális hálózat Azure-beli virtuális hálózati átjáróhoz (ExpressRoute vagy VPN-hez) való kapcsolódása után irányítja. Csak olyan alhálózatokhoz rendelhet útválasztási táblázatot, amelyek ugyanabban az Azure-helyen és-előfizetésben találhatók, mint az útválasztási táblázat.

1. A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

1. A virtuális hálózat listán válassza ki azt az alhálózatot tartalmazó virtuális hálózatot, amelyhez hozzá szeretné rendelni az útválasztási táblázatot.

1. A virtuális hálózat menüsávján válassza az **alhálózatok** lehetőséget.

1. Válassza ki azt az alhálózatot, amelyhez az útválasztási táblázatot hozzá szeretné rendelni.

1. Az **útválasztási táblázatban** válassza ki azt az útválasztási táblázatot, amelyet az alhálózathoz kíván rendelni.

1. Kattintson a **Mentés** gombra.

Ha a virtuális hálózat egy Azure VPN Gateway-átjáróhoz csatlakozik, ne társítson útválasztási táblázatot az [átjáró-alhálózathoz](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) , amely tartalmazza a *0.0.0.0/0* rendeltetésű útvonalat. Ellenkező esetben előfordulhat, hogy az átjárószolgáltatás nem működik megfelelően. További információ a *0.0.0.0/0* útvonalon való használatáról: [virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Útválasztási táblázat hozzárendelése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Útválasztási táblázat leválasztása egy alhálózatból

Amikor alhálózatból választ ki egy útválasztási táblázatot, az Azure az [alapértelmezett útvonalak](virtual-networks-udr-overview.md#default)alapján irányítja a forgalmat.

1. A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

1. A virtuális hálózat listán válassza ki azt az alhálózatot tartalmazó virtuális hálózatot, amelyről el szeretné választani az útválasztási táblázatot.

1. A virtuális hálózat menüsávján válassza az **alhálózatok** lehetőséget.

1. Válassza ki azt az alhálózatot, amelyből el szeretné választani az útválasztási táblázatot.

1. Az **útválasztási táblázatban** válassza a **nincs** lehetőséget.

1. Kattintson a **Mentés** gombra.

### <a name="dissociate-a-route-table---commands"></a>Útválasztási táblázat leválasztása – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Útválasztási táblázat törlése

Nem törölhet olyan útválasztási táblázatot, amely egyetlen alhálózathoz van társítva. [A törlés](#dissociate-a-route-table-from-a-subnet) megkísérlése előtt szüntesse meg az összes alhálózatból származó útválasztási táblázatot.

1. Az útválasztási táblázatok kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listában válassza ki a törölni kívánt útválasztási táblázatot.

1. Válassza a **Törlés** lehetőséget, majd a megerősítő párbeszédpanelen válassza az **Igen** lehetőséget.

### <a name="delete-a-route-table---commands"></a>Útválasztási táblázat törlése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Útvonal létrehozása

Az útválasztási táblázatokban az Azure-beli hely és az előfizetés alapján több útvonal is létrehozható. Részletekért lásd: [hálózati korlátok – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Az útválasztási táblázatok kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listából válassza ki azt az útválasztási táblázatot, amelyhez hozzá kíván adni egy útvonalat.

1. Az útválasztási táblázat menüsávján válassza az **útvonalak**  >  **Hozzáadás** lehetőséget.

1. Adja meg az útvonal egyedi **útvonalának nevét** az útválasztási táblázatban.

1. Adja meg a **címnek** az osztály nélküli Inter-Domain ÚTVÁLASZTÁSI (CIDR) jelölését, amelynek a forgalmát át szeretné irányítani. Az előtag nem duplikálható egynél több útvonalon az útválasztási táblában, bár az előtag egy másik előtagjan belül is lehet. Ha például *10.0.0.0/16* előtagot adott meg egy útvonalon, akkor továbbra is definiálhat egy másik útvonalat a *10.0.0.0/22-* előtaggal. Az Azure a leghosszabb előtag-egyezés alapján kiválasztja a forgalom útvonalát. További információ: [how Azure kiválasztja az útvonalat](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Válasszon egy **következő ugrási típust**. További információ a következő ugrási típusokról: [virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).

1. Ha a **virtuális berendezés** **következő ugrási típusát** választotta, adjon meg egy IP-címet a **következő ugrási címhez**.

1. Válassza az **OK** lehetőséget.

### <a name="create-a-route---commands"></a>Útvonal-parancsok létrehozása

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [Új – AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Útvonalak megtekintése

Az útválasztási táblázat nulla vagy több útvonalat tartalmaz. Ha többet szeretne megtudni az útvonalak megtekintésekor felsorolt információkkal kapcsolatban, tekintse meg a [virtuális hálózati forgalom útválasztását](virtual-networks-udr-overview.md)ismertető témakört.

1. Az útválasztási táblázatok kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listából válassza ki azt az útválasztási táblázatot, amelyre vonatkozóan meg szeretné tekinteni az útvonalakat.

1. Az útválasztási táblázat menüsávján válassza az **útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

### <a name="view-routes---commands"></a>Útvonalak megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route List](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. Az útválasztási táblázatok kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listából válassza ki azt az útválasztási táblázatot, amely tartalmazza az útvonalat, amelyről meg szeretné tekinteni a részleteket.

1. Az útválasztási táblázat menüsávján válassza az **útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

1. Válassza ki azt az útvonalat, amelynek adatait meg szeretné tekinteni.

### <a name="view-details-of-a-route---commands"></a>Route-parancsok részleteinek megtekintése

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Útvonal módosítása

1. Az útválasztási táblázatok kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listában válassza ki a módosítani kívánt útvonalat tartalmazó útválasztási táblázatot.

1. Az útválasztási táblázat menüsávján válassza az **útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

1. Válassza ki a módosítani kívánt útvonalat.

1. Módosítsa a meglévő beállításokat az új beállításokra, majd válassza a **Mentés** lehetőséget.

### <a name="change-a-route---commands"></a>Route-parancsok módosítása

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route Update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Útvonal törlése

1. Az útválasztási táblázatok kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Az útválasztási táblázat listában válassza ki a törölni kívánt útvonalat tartalmazó útválasztási táblázatot.

1. Az útválasztási táblázat menüsávján válassza az **útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

1. Válassza ki a törölni kívánt útvonalat.

1. Válassza a **Törlés** lehetőséget, majd válassza az **Igen** lehetőséget a megerősítő párbeszédpanelen.

### <a name="delete-a-route---commands"></a>Útvonal törlése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Route-Table Route delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Érvényes útvonalak megtekintése

Az egyes virtuális gépekhez csatlakoztatott hálózati adapterek érvényes útvonalai a létrehozott útválasztási táblák, az Azure alapértelmezett útvonalai, valamint a helyszíni hálózatokról a Border Gateway Protocol (BGP) használatával propagált útvonalak, amelyek egy Azure-beli virtuális hálózati átjárón keresztül továbbítódnak. A hálózati adapterek hatékony útvonalának megismerése az útválasztási problémák elhárításakor hasznos. Megtekintheti a futó virtuális géphez csatlakoztatott bármely hálózati adapter érvényes útvonalait.

1. A virtuális gépek kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

1. A virtuális gép listából válassza ki azt a virtuális gépet, amely számára a hatályos útvonalakat meg szeretné tekinteni.

1. A virtuális gép menüsorában válassza a **hálózatkezelés** lehetőséget.

1. Válassza ki a hálózati adapter nevét.

1. A hálózati adapter menüsávján válassza az **érvényes útvonalak** elemet.

1. Tekintse át az érvényes útvonalak listáját, és ellenőrizze, hogy a megfelelő útvonal létezik-e, ahová a forgalmat át szeretné irányítani. További információ a következő ugrási típusokról, amelyeket ebben a listában a [virtuális hálózati forgalom útválasztásában](virtual-networks-udr-overview.md)talál.

### <a name="view-effective-routes---commands"></a>Érvényes útvonalak megtekintése – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NIC show-effektív-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Két végpont közötti útválasztás ellenőrzése

Megadhatja a következő ugrás típusát egy virtuális gép és egy másik Azure-erőforrás, egy helyszíni erőforrás vagy egy internetes erőforrás IP-címe között. Az útválasztási problémák elhárítása érdekében az Azure útválasztásának meghatározása hasznos. A feladat végrehajtásához rendelkeznie kell egy meglévő hálózati figyelővel. Ha nem rendelkezik meglévő hálózati figyelővel, hozzon létre egyet a [Network Watcher példány létrehozása](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben leírt lépések végrehajtásával.

1. A hálózati figyelők kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **Network Watcher**.

1. A Network Watcher menüsávban válassza a **következő ugrás** lehetőséget.

1. A **Network Watcherban | Következő ugrás** oldal:

    1. Válassza ki az **előfizetését** és annak a forrás virtuális gépnek az **erőforrás-csoportját** , amelyről az útválasztást érvényesíteni szeretné.

    1. Válassza ki a **virtuális gépet** és a virtuális géphez csatlakoztatott **hálózati adaptert** .
    
    1. Adja meg a hálózati adapterhez hozzárendelt **forrás IP-címet** , amelyről az útválasztást érvényesíteni szeretné.

    1. Adja meg a **cél IP-címet** , amelyre érvényesíteni szeretné az útválasztást.

1. Válassza a **következő ugrás** lehetőséget.

Rövid várakozás után az Azure megadja a következő ugrás típusát és a forgalmat átirányító útvonal AZONOSÍTÓját. További információ a [virtuális hálózati forgalom útválasztásának](virtual-networks-udr-overview.md)következő ugrási típusairól:.

### <a name="validate-routing-between-two-endpoints---commands"></a>Útválasztás ellenőrzése két végpont között – parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network Watcher show-Next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Engedélyek

A táblák és útvonalak útválasztási feladatainak elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Művelet                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/READ                              |   Útválasztási táblázat beolvasása                                    |
| Microsoft. Network/routeTables/Write                             |   Útválasztási táblázat létrehozása vagy frissítése                        |
| Microsoft. Network/routeTables/delete                            |   Útválasztási táblázat törlése                                  |
| Microsoft. Network/routeTables/csatlakozás/művelet                       |   Útválasztási táblázat társítása alhálózattal                   |
| Microsoft. Network/routeTables/Routes/READ                       |   Útvonal beolvasása                                          |
| Microsoft. Network/routeTables/Routes/Write                      |   Útvonal létrehozása vagy frissítése                              |
| Microsoft. Network/routeTables/Routes/delete                     |   Útvonal törlése                                        |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/Action  |   A hálózati adapter érvényes útválasztási táblázatának beolvasása |
| Microsoft. Network/networkWatchers/nextHop/Action                |   Egy virtuális gép következő ugrásának beolvasása                           |

## <a name="next-steps"></a>Következő lépések

- Útválasztási táblázat létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) parancsfájlokkal vagy Azure [Resource Manager-sablonokkal](template-samples.md)
- [Azure Policy-definíciók](./policy-reference.md) létrehozása és társítása virtuális hálózatokhoz