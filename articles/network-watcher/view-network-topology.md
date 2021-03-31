---
title: Azure-beli virtuális hálózati topológia megtekintése | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg a virtuális hálózatok erőforrásait és az erőforrások közötti kapcsolatokat.
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
ms.openlocfilehash: c04da65af27ebd5ac654bc059ae004c157a20f33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737529"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Azure-beli virtuális hálózat topológiájának megtekintése

Ebből a cikkből megtudhatja, hogyan tekintheti meg az erőforrásokat egy Microsoft Azure virtuális hálózatban, valamint az erőforrások közötti kapcsolatokat. Egy virtuális hálózat például alhálózatokat tartalmaz. Az alhálózatok olyan erőforrásokat tartalmaznak, mint például az Azure Virtual Machines (VM). A virtuális gépek egy vagy több hálózati adapterrel rendelkeznek. Minden alhálózathoz tartozhat egy hálózati biztonsági csoport és egy útválasztási táblázat is. Az Azure Network Watcher topológiai funkciója lehetővé teszi egy virtuális hálózat összes erőforrásának megtekintését, a virtuális hálózat erőforrásaihoz társított erőforrásokat, valamint az erőforrások közötti kapcsolatokat.

Az [Azure Portal](#azure-portal), az [Azure CLI](#azure-cli)vagy a [PowerShell](#powershell) használatával megtekintheti a topológiát.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topológia megtekintése – Azure Portal

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely rendelkezik a szükséges [engedélyekkel](required-rbac-permissions.md).
2. A portál bal felső sarkában válassza a **minden szolgáltatás** lehetőséget.
3. A **minden szolgáltatás** szűrő mezőbe írja be a *Network Watcher*. Ha **Network Watcher** jelenik meg az eredmények között, válassza ki.
4. Válassza a **Topológia** lehetőséget. A topológia létrehozásához egy olyan hálózati figyelőre van szükség, amely ugyanabban a régióban található, mint ahol az a virtuális hálózat, amelyhez a topológiát létre kívánja hozni. Ha nincs engedélyezve a hálózati figyelő abban a régióban, amelyben az a virtuális hálózat, amelyhez a topológiát létre szeretné hozni, a hálózati figyelők automatikusan létrejönnek az összes régióban. A hálózati figyelők egy **NetworkWatcherRG** nevű erőforráscsoporthoz jönnek létre.
5. Válasszon ki egy előfizetést, egy virtuális hálózat erőforráscsoporthoz, amelyben meg szeretné tekinteni a topológiát, majd válassza ki a virtuális hálózatot. Az alábbi ábrán egy *MyVnet* nevű virtuális hálózat topológiája látható a *MyResourceGroup* nevű erőforráscsoport esetében:

    ![Topológia megtekintése](./media/view-network-topology/view-topology.png)

    Ahogy az előző képen is látható, a virtuális hálózat három alhálózatot tartalmaz. Az egyik alhálózaton telepítve van egy virtuális gép. A virtuális gépnek egyetlen hálózati adaptere van, amelyhez hozzá van rendelve egy nyilvános IP-cím. A másik két alhálózathoz hozzá van rendelve egy útválasztási táblázat. Minden útválasztási tábla két útvonalat tartalmaz. Az egyik alhálózathoz hozzá van rendelve egy hálózati biztonsági csoport. A topológia adatai csak a következő erőforrásokra mutatnak:
    
    - A *myVnet* virtuális hálózattal megegyező erőforráscsoport és régió között. Például egy olyan hálózati biztonsági csoport, amely nem *MyResourceGroup*, nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat egyik alhálózatához van társítva.
    - A *myVnet* virtuális hálózatán belül vagy ahhoz tartozó erőforrásokhoz társítva. Például egy olyan hálózati biztonsági csoport, amely nincs társítva a *myVnet* virtuális hálózat alhálózatához vagy hálózati adapteréhez, nem jelenik meg, még akkor is, ha a hálózati biztonsági csoport a *MyResourceGroup* -erőforráscsoport tagja.

   A képen látható topológia ahhoz a virtuális hálózathoz lett létrehozva, amelyet az **útvonal forgalmának** az [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)-vel vagy a [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)használatával történő üzembe helyezése után hozott létre.

6. Válassza a **topológia letöltése** lehetőséget a rendszerkép szerkeszthető fájlként való letöltéséhez SVG formátumban.

A diagramon látható erőforrások a virtuális hálózat hálózatkezelési összetevőinek egy részhalmaza. Ha például megjelenik egy hálózati biztonsági csoport, a benne található biztonsági szabályok nem jelennek meg a diagramon. Bár a diagramon nem különböznek egymástól, a sorok a két kapcsolat egyikét jelölik: a *tárolást* vagy a *társítást*. A virtuális hálózaton található erőforrások teljes listájának, valamint az erőforrások közötti kapcsolat típusának megtekintéséhez a topológiát a PowerShell vagy az [Azure CLI](#azure-cli) [használatával](#powershell) hozhatja meg.

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topológia megtekintése – Azure CLI

A következő lépésekben futtathatja a parancsokat:
- A Azure Cloud Shell a bármely parancs jobb felső sarkában lévő **kipróbálás** lehetőség kiválasztásával. A Azure Cloud Shell egy olyan ingyenes interaktív felület, amelynek általános Azure-eszközei előre telepítve és konfigurálva vannak a fiókjával való használatra.
- A CLI-t a számítógépről futtatva. Ha a parancssori felületet a számítógépről futtatja, a cikkben ismertetett lépéseket az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell, `az login` hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A használt fióknak rendelkeznie kell a szükséges [engedélyekkel](required-rbac-permissions.md).

1. Ha már van hálózati figyelő abban a régióban, ahol a virtuális hálózatot szeretné létrehozni, ugorjon a 3. lépésre. Hozzon létre egy erőforráscsoportot, amely az [az Group Create](/cli/azure/group)paranccsal hálózati figyelőt tartalmaz. A következő példa létrehozza az erőforráscsoportot a *eastus* régióban:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Hozzon létre egy hálózati figyelőt az [az Network Watcher configure](/cli/azure/network/watcher#az-network-watcher-configure)paranccsal. A következő példa egy Network watchert hoz létre a *eastus* régióban:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. A topológia megtekintése az [az Network Watcher show-topológia](/cli/azure/network/watcher#az-network-watcher-show-topology)paranccsal. A következő példa egy *MyResourceGroup* nevű erőforráscsoport topológiáját tekinti át:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    A topológiai adatok csak olyan erőforrásokhoz lesznek visszaadva, amelyek ugyanabban az erőforráscsoporthoz tartoznak, mint a *MyResourceGroup* -erőforráscsoport, valamint a hálózati figyelővel megegyező régió. Például egy olyan hálózati biztonsági csoport, amely nem *MyResourceGroup*, nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat egyik alhálózatához van társítva.

   További információ a visszaadott kimenet kapcsolatairól és [tulajdonságairól](#properties) . Ha nem rendelkezik meglévő virtuális hálózattal a topológia megtekintéséhez, létrehozhat egyet a [hálózati virtuális berendezés](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) parancsfájl-mintáján keresztül. A topológia diagramjának megtekintéséhez és egy szerkeszthető fájlban való letöltéséhez használja a [portált](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topológia megtekintése – PowerShell

A következő lépésekben futtathatja a parancsokat:
- A Azure Cloud Shell a bármely parancs jobb felső sarkában lévő **kipróbálás** lehetőség kiválasztásával. A Azure Cloud Shell egy olyan ingyenes interaktív felület, amelynek általános Azure-eszközei előre telepítve és konfigurálva vannak a fiókjával való használatra.
- A PowerShell futtatásával a számítógépről. Ha a PowerShellt a számítógépről futtatja, akkor ehhez a cikkhez a Azure PowerShell `Az` modulra van szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

A használt fióknak rendelkeznie kell a szükséges [engedélyekkel](required-rbac-permissions.md).

1. Ha már van hálózati figyelő abban a régióban, ahol a virtuális hálózatot szeretné létrehozni, ugorjon a 3. lépésre. Hozzon létre egy erőforráscsoportot, amely egy [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)rendelkező Network watchert tartalmaz. A következő példa létrehozza az erőforráscsoportot a *eastus* régióban:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Hozzon létre egy Network watchert a [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). A következő példa egy Network watchert hoz létre a eastus régióban:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Network Watcher példány beolvasása a [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). A következő példa egy Network watchert kér le az USA keleti régiójában:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Topológia beolvasása a [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). A következő példa egy *MyResourceGroup* nevű erőforráscsoport egy virtuális hálózatának topológiáját kérdezi le:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   A topológiai adatok csak olyan erőforrásokhoz lesznek visszaadva, amelyek ugyanabban az erőforráscsoporthoz tartoznak, mint a *MyResourceGroup* -erőforráscsoport, valamint a hálózati figyelővel megegyező régió. Például egy olyan hálózati biztonsági csoport, amely nem *MyResourceGroup*, nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat egyik alhálózatához van társítva.

   További információ a visszaadott kimenet kapcsolatairól és [tulajdonságairól](#properties) . Ha nem rendelkezik meglévő virtuális hálózattal a topológia megtekintéséhez, létrehozhat egyet a [hálózati virtuális berendezés](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) parancsfájl-mintáján keresztül. A topológia diagramjának megtekintéséhez és egy szerkeszthető fájlban való letöltéséhez használja a [portált](#azure-portal).

## <a name="relationships"></a>Kapcsolatok

A topológiában visszaadott összes erőforráshoz a következő típusú kapcsolatok egyike tartozik egy másik erőforráshoz:

| Kapcsolattípus | Példa                                                                                                |
| ---               | ---                                                                                                    |
| Befoglaltság       | A virtuális hálózat alhálózatot tartalmaz. Az alhálózat egy hálózati adaptert tartalmaz.                            |
| Társítva        | Egy hálózati adapter társítva van egy virtuális géphez. A nyilvános IP-cím egy hálózati adapterhez van társítva. |

## <a name="properties"></a>Tulajdonságok

A topológiában visszaadott összes erőforrás a következő tulajdonságokkal rendelkezik:

- **Name (név**): az erőforrás neve
- **Azonosító**: az erőforrás URI-ja.
- **Hely**: az az Azure-régió, amelyen az erőforrás megtalálható.
- **Társítások**: a hivatkozott objektumhoz tartozó társítások listája. Minden társítás a következő tulajdonságokkal rendelkezik:
    - **AssociationType társítás**: a gyermek objektum és a szülő közötti kapcsolatra hivatkozik. Az érvényes értékek *tartalmazzák* vagy *társítva* vannak.
    - **Name (név**): a hivatkozott erőforrás neve.
    - **ResourceId**: – a társításban hivatkozott erőforrás URI-ja.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [diagnosztizálhatja a hálózati forgalom szűrésével kapcsolatos problémát egy virtuális](diagnose-vm-network-traffic-filtering-problem.md) gépről Network Watcher IP-forgalmának ellenőrzése funkcióval
- Ismerje meg, hogyan [diagnosztizálhatja a hálózati forgalom-útválasztási problémát egy virtuális](diagnose-vm-network-routing-problem.md) gépről a Network Watcher következő ugrási képességének használatával
