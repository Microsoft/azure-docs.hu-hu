---
title: Azure-beli virtuális hálózati topológia | Microsoft Docs
description: Megtudhatja, hogyan lehet megtekinteni egy virtuális hálózat erőforrásait és az erőforrások közötti kapcsolatokat.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: f20fa22dac3fba4d01cbc5e398bafa4113e94a96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780298"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Azure-beli virtuális hálózat topológiája

Ebből a cikkből megtudhatja, hogyan lehet megtekinteni egy virtuális hálózaton Microsoft Azure erőforrásokat, valamint az erőforrások közötti kapcsolatokat. Egy virtuális hálózat például alhálózatokat tartalmaz. Az alhálózatok olyan erőforrásokat tartalmaznak, mint az Azure Virtual Machines (VM). A virtuális gépek egy vagy több hálózati adaptert is illesztők. Minden alhálózathoz egy hálózati biztonsági csoport és egy útválasztási táblázat társítva lehet. Az Azure Network Watcher topológia képessége lehetővé teszi a virtuális hálózatban lévő összes erőforrás, a virtuális hálózatban lévő erőforrásokhoz társított erőforrások és az erőforrások közötti kapcsolatok megtekintését.

A topológiák megtekintéséhez [használhatja](#azure-portal)Azure Portal, [az Azure CLI-t](#azure-cli)vagy a [PowerShellt.](#powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topológia megtekintése – Azure Portal

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy olyan fiókkal, amely rendelkezik a szükséges [engedélyekkel.](required-rbac-permissions.md)
2. A portál bal felső sarkában válassza a **Minden szolgáltatás lehetőséget.**
3. A Minden **szolgáltatás szűrőmezőbe** írja be a *következőt: Network Watcher.* Amikor **Network Watcher** megjelenik az eredmények között, válassza ki.
4. Válassza a **Topológia** lehetőséget. A topológia létrehozásához hálózati figyelőre van szükség ugyanabban a régióban, ahol a virtuális hálózat, amelyhez létre szeretné hozni a topológiát, már létezik. Ha nincs engedélyezve network watcher abban a régióban, ahol a virtuális hálózatot létre szeretné hozni, a hálózati figyelők automatikusan létrejönnek az összes régióban. A hálózati figyelők egy **NetworkWatcherRG** nevű erőforráscsoportban vannak létrehozva.
5. Válasszon ki egy előfizetést, annak a virtuális hálózatnak az erőforráscsoportját, amely a topológiáját meg szeretné tekinteni, majd válassza ki a virtuális hálózatot. Az alábbi képen egy *MyVnet* nevű virtuális hálózat topológiája látható a *MyResourceGroup nevű erőforráscsoportban:*

    ![Topológia megtekintése](./media/view-network-topology/view-topology.png)

    Ahogy az előző képen is látható, a virtuális hálózat három alhálózatot tartalmaz. Az egyik alhálózatban egy virtuális gép van üzembe helyezni. A virtuális géphez egy hálózati adapter van csatlakoztatva, és hozzá van társítva egy nyilvános IP-cím. A másik két alhálózathoz egy útválasztási táblázat van társítva. Minden útvonaltábla két útvonalat tartalmaz. Az egyik alhálózathoz hálózati biztonsági csoport van társítva. A topológiai információk csak a következő erőforrások esetén jelennek meg:
    
    - Ugyanabban az erőforráscsoportban és régióban, ahol a *myVnet virtuális* hálózat található. Például a *MyResourceGroup* erőforráscsoporton kívül található hálózati biztonsági csoport nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a MyVnet virtuális hálózat egyik *alhálózatához* van társítva.
    - A *myVnet* virtuális hálózaton belül, vagy az erőforrásokhoz társítva. Például egy olyan hálózati biztonsági csoport, amely nincs alhálózathoz vagy hálózati adapterhez társítva a *myVnet* virtuális hálózatban, akkor sem jelenik meg, ha a hálózati biztonsági csoport a *MyResourceGroup* erőforráscsoportban található.

   A képen látható topológia a Forgalom útválasztása egy hálózati virtuális berendezésen keresztüli üzembe helyezése után létrehozott virtuális hálózatra, amelyet az [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)vagy **a** PowerShell használatával helyezhet [üzembe.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

6. Válassza **a Topológia letöltése lehetőséget** a kép letölthető fájlként svg formátumban való letöltéséhez.

A diagramon látható erőforrások a virtuális hálózat hálózati összetevőinek részkészletei. Ha például egy hálózati biztonsági csoport jelenik meg, a benne található biztonsági szabályok nem jelennek meg a diagramon. Bár a diagram nem tesz különbséget, a vonalak a következő két kapcsolat valamelyikét képviselik: *Tartalmazottság* vagy *társított*. A virtuális hálózatban lévő erőforrások teljes listájának és az erőforrások közötti kapcsolat típusának a [powershell](#powershell) vagy az Azure CLI használatával generálja a [topológiát.](#azure-cli)

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topológia megtekintése – Azure CLI

A következő lépésekben futtathatja a parancsokat:
- A Azure Cloud Shell a bármely parancs jobb felső részen található Try **It** (Kipróbálom) lehetőség kiválasztásával. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely előre telepített és a fiókjával való használatra konfigurált általános Azure-eszközöket tartalmaz.
- A CLI a számítógépről való futtatásával. Ha a CLI-t a számítógépről futtatja, a cikkben található lépésekhez az Azure CLI 2.0.31-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha az Azure CLI-t helyileg futtatja, akkor a futtatásával kapcsolatot kell `az login` létesítenie az Azure-ral.

A használt fióknak rendelkeznie kell a szükséges [engedélyekkel.](required-rbac-permissions.md)

1. Ha már rendelkezik network watcherrel ugyanabban a régióban, mint a virtuális hálózat, amely számára létre szeretné hozni a topológiát, ugorjon a 3. lépésre. Hozzon létre egy hálózati figyelőt tartalmazó erőforráscsoportot [az az group create segítségével.](/cli/azure/group) Az alábbi példa az *eastus* régióban hozza létre az erőforráscsoportot:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Hozzon létre egy Network Watchert [az az network watcher configure beállítással.](/cli/azure/network/watcher#az_network_watcher_configure) Az alábbi példa egy Network Watchert hoz létre az *eastus régióban:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Tekintse meg a topológiát [az az network watcher show-topology figyelővel.](/cli/azure/network/watcher#az_network_watcher_show_topology) Az alábbi példa egy *MyResourceGroup* nevű erőforráscsoport topológiáját nézi meg:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    A rendszer csak olyan erőforrások topológiai adatait ad vissza, amelyek a *MyResourceGroup* erőforráscsoporttal és a Network Watcherrel azonos régióban találhatóak. A *MyResourceGroup* erőforráscsoporton kívül található hálózati biztonsági csoport például akkor sem jelenik meg, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat egyik alhálózatához van társítva.

   A visszaadott kimenetben további [információt olvashat](#properties) a kapcsolatokról és a tulajdonságokról. Ha nincs meglévő virtuális hálózata a topológiájának megtekintéséhez, létrehozhat egyet a Forgalom útválasztása hálózati virtuális berendezésen keresztül [szkriptmintával.](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) A topológia diagramjának megtekintéséhez és szerkeszthető fájlba való letöltéséhez használja a [portált.](#azure-portal)

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topológia megtekintése – PowerShell

A parancsokat a következő lépésekben futtathatja:
- A Azure Cloud Shell a bármely parancs jobb felső részen található Try **It** (Próbálja ki) lehetőség választásával. A Azure Cloud Shell egy ingyenes interaktív felület, amely előre telepített és a fiókjával való használatra konfigurált általános Azure-eszközöket tartalmaz.
- Futtatja a PowerShellt a számítógépről. Ha a PowerShellt a számítógépről futtatja, ehhez a cikkhez szükség van a Azure PowerShell `Az` modulra. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

A használt fióknak rendelkeznie kell a szükséges [engedélyekkel.](required-rbac-permissions.md)

1. Ha már rendelkezik hálózati figyelővel abban a régióban, ahol a virtuális hálózat, ahol létre szeretné hozni a topológiát, ugorjon a 3. lépésre. Hozzon létre egy hálózati figyelőt tartalmazó erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.Resources/New-azResourceGroup) Az alábbi példa az erőforráscsoportot az *eastus régióban hozza* létre:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Hozzon létre egy network watchert a [New-AzNetworkWatcher segítségével.](/powershell/module/az.network/new-aznetworkwatcher) Az alábbi példa egy Network Watchert hoz létre az eastus régióban:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Egy Network Watcher a [Get-AzNetworkWatcher segítségével.](/powershell/module/az.network/get-aznetworkwatcher) Az alábbi példa lekér egy network watchert az USA keleti régiójában:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Topológia lekérése a [Get-AzNetworkWatcherTopology segítségével.](/powershell/module/az.network/get-aznetworkwatchertopology) Az alábbi példa egy *myResourceGroup* nevű erőforráscsoportban lévő virtuális hálózat topológiáját olvassa be:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   A rendszer csak olyan erőforrások topológiai információit ad vissza, amelyek a *MyResourceGroup* erőforráscsoporttal azonos erőforráscsoportban és a Network Watcherrel azonos régióban vannak. Például a *MyResourceGroup* erőforráscsoporton kívül található hálózati biztonsági csoport nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a MyVnet virtuális hálózat egyik *alhálózatához* van társítva.

   A visszaadott kimenetben további [információt olvashat](#properties) a kapcsolatokról és a tulajdonságokról. Ha nincs meglévő virtuális hálózata a topológiák megtekintéséhez, létrehozhat egyet a Forgalom útválasztása hálózati virtuális berendezésen keresztül [szkriptmintával.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) A topológia diagramjának megtekintéséhez és szerkeszthető fájlba való letöltéséhez használja a [portált.](#azure-portal)

## <a name="relationships"></a>Kapcsolatok

A topológiában visszaadott összes erőforrás a következő típusú kapcsolatban áll egy másik erőforrással:

| Kapcsolattípus | Példa                                                                                                |
| ---               | ---                                                                                                    |
| Befoglaltság       | A virtuális hálózat tartalmaz egy alhálózatot. Az alhálózatok hálózati adaptert tartalmaznak.                            |
| Társítva        | A hálózati adapter egy virtuális géphez van társítva. A nyilvános IP-cím egy hálózati adapterhez van társítva. |

## <a name="properties"></a>Tulajdonságok

A topológiában visszaadott összes erőforrás a következő tulajdonságokkal rendelkezik:

- **Név:** Az erőforrás neve
- **Id**: Az erőforrás URI-azonosítója.
- **Hely:** Az az Azure-régió, ahol az erőforrás megtalálható.
- **Társítások:** A hivatkozott objektumhoz való társítások listája. Minden társítás a következő tulajdonságokkal rendelkezik:
    - **AssociationType:** A gyermekobjektum és a szülő közötti kapcsolatra hivatkozik. Érvényes értékek: *Contains (Tartalmazza)* vagy *Associated (Társított).*
    - **Név:** A hivatkozott erőforrás neve.
    - **ResourceId:**– A társításban hivatkozott erőforrás URI-ja.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, [hogyan diagnosztizálhatja a](diagnose-vm-network-traffic-filtering-problem.md) virtuális gépekre vagy gépekről a hálózati forgalom szűrésével kapcsolatos problémákat Network Watcher IP-forgalom ellenőrzésére vonatkozó képességével
- Megtudhatja, [hogyan diagnosztizálhatja egy](diagnose-vm-network-routing-problem.md) virtuális gép hálózati forgalmának útválasztási problémáját a Network Watcher következő ugrás funkcióval
