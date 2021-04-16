---
title: Hálózati biztonsági csoport diagnosztikai erőforrás-naplózása
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan engedélyezheti az esemény- és szabályszámlálók diagnosztikai erőforrásnaplóit egy Azure-beli hálózati biztonsági csoport számára.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 42ce7a1760ecdb1dcbd5275927f351bef5da07a8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531166"
---
# <a name="resource-logging-for-a-network-security-group"></a>Hálózati biztonsági csoport erőforrás-naplózása

A hálózati biztonsági csoport (NSG) olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a virtuális hálózat alhálózatának, hálózati adapterének vagy mindkettőnek a forgalmát. 

Amikor engedélyezi a naplózást egy NSG számára, a következő típusú erőforrásnapló-információkat gyűjtheti:

* **Esemény:** A rendszer naplózza a bejegyzéseket, amelyekre a rendszer a MAC-cím alapján NSG-szabályokat alkalmaz a virtuális gépeken.
* **Szabályszámláló:** Bejegyzéseket tartalmaz arra, hogy a rendszer hányszor alkalmazza az egyes NSG-szabályokat a forgalom megtagadása vagy engedélyezése érdekében. A szabályok állapota 300 másodpercenként lesz összegyűjtve.

Az erőforrásnaplók csak az üzembe helyezési modellel üzembe helyezett NSG-k Azure Resource Manager érhetők el. A klasszikus üzembe helyezési modellel üzembe helyezett NSG-k esetében nem engedélyezhető az erőforrás-naplózás. A két modell jobb megértéséhez lásd: Understanding Azure deployment models (Az Azure üzembe [helyezési modelljeinek ismertetése).](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Az erőforrás-naplózás külön engedélyezve van *minden* olyan NSG-hez, amelyről diagnosztikai adatokat szeretne gyűjteni. Ha inkább a tevékenységnaplók (működési) naplók érdeklik, tekintse meg az [Azure-tevékenységnaplózást.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha érdekli az NSG-n áthaladó IP-forgalom, tekintse meg az Azure Network Watcher [NSG-forgalom naplóit](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 

## <a name="enable-logging"></a>Naplózás engedélyezése

Az erőforrás-naplózás [engedélyezéséhez](#azure-portal)használhatja az Azure Portalt, a [PowerShellt](#powershell)vagy az [Azure CLI-t.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [portálra.](https://portal.azure.com)
2. Válassza **a Minden szolgáltatás** lehetőséget, majd írja be a hálózati biztonsági csoportok *parancsot.* Amikor **a hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
3. Válassza ki azt az NSG-t, amely számára engedélyezni szeretné a naplózást.
4. A **FIGYELÉS** alatt válassza a **Diagnosztikai naplók** lehetőséget, majd válassza a **Diagnosztika bekapcsolása** lehetőséget, ahogy az alábbi képen is látható:

   ![Diagnosztika bekapcsolása](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. A **Diagnosztikai beállítások alatt** adja meg vagy válassza ki a következő adatokat, majd válassza a Mentés **lehetőséget:**

    | Beállítás                                                                                     | Érték                                                          |
    | ---------                                                                                   |---------                                                       |
    | Név                                                                                        | A választott név.  Például: *myNsgDiagnostics*      |
    | **Archiválás tárfiókba,** **Streamelés eseményközpontba,** majd Küldés a **Log Analyticsbe** | Annyi célhelyet választhat ki, amennyit csak választ. További információ az egyes célokkal kapcsolatban: [Naplócélok.](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Válassza ki az egyik vagy mindkét naplózási kategóriát. További információ az egyes kategóriákhoz naplózott adatokról: [Naplókategóriák.](#log-categories)                                                                                                                                             |
6. Naplók megtekintése és elemzése. További információ: [Naplók megtekintése és elemzése.](#view-and-analyze-logs)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancsokat futtathatja a [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. A Azure Cloud Shell egy ingyenes interaktív felület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell 1.0.0-s vagy újabb verziójára. A `Get-Module -ListAvailable Az` telepített verzió megkereséhez futtassa a következőt a számítógépen: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a futtatásával is be kell jelentkeznie az Azure-ba egy olyan fiókkal, amely rendelkezik a szükséges `Connect-AzAccount` [engedélyekkel.](virtual-network-network-interface.md#permissions)

Az erőforrás-naplózás engedélyezéséhez szüksége lesz egy meglévő NSG azonosítójára. Ha még nem létezik NSG, létrehozhat egyet a [New-AzNetworkSecurityGroup segítségével.](/powershell/module/az.network/new-aznetworksecuritygroup)

A [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)segítségével lekéri azt a hálózati biztonsági csoportot, amely számára engedélyezni szeretné az erőforrás-naplózást. Ha például egy *myNsg* nevű NSG-t kell lekérni egy *myResourceGroup* nevű erőforráscsoportban, írja be a következő parancsot:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Az erőforrásnaplókat három céltípusba írhatja. További információ: [Naplócélok.](#log-destinations) Ebben a cikkben például a naplók a Log Analytics-célhelyre vannak elküldve.  Meglévő Log Analytics-munkaterület lekérése a [Get-AzOperationalInsightsWorkspace parancs használatával.](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) Ha például egy myWorkspaces nevű erőforráscsoportban *lévő, myWorkspaces* nevű meglévő munkaterületet *kell* lekérni, írja be a következő parancsot:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Ha még nem található munkaterülete, létrehozhat egyet a [New-AzOperationalInsightsWorkspace használatával.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)

A naplózásnak két kategóriája van, amelyekhez engedélyezheti a naplókat. További információ: [Naplókategóriák.](#log-categories) Engedélyezze az erőforrás-naplózást az NSG-hez a [Set-AzDiagnosticSetting parancs segítségével.](/powershell/module/az.monitor/set-azdiagnosticsetting) Az alábbi példa egy NSG-hez tartozó esemény- és számlálókategória-adatokat is naplózza az NSG-hez tartozó munkaterületen a korábban lekért NSG és munkaterületazonosítók használatával:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Ha nem csak az egyik kategóriához vagy a másikhoz szeretne adatokat naplózni, adja hozzá a kapcsolót az előző parancshoz, majd a `-Categories` *NetworkSecurityGroupEvent* vagy *a NetworkSecurityGroupRuleCounter parancshoz.* Ha a Log Analytics-munkaterülettől eltérő célhelyre szeretne bejelentkezni, használja a megfelelő paramétereket egy Azure [Storage-fiókhoz](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) vagy [eseményközponthoz.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) [](#log-destinations)

Naplók megtekintése és elemzése. További információ: [Naplók megtekintése és elemzése.](#view-and-analyze-logs)

### <a name="azure-cli"></a>Azure CLI

A következő parancsokat futtathatja a [Azure Cloud Shell,](https://shell.azure.com/bash)vagy az Azure CLI a számítógépről való futtatásával. A Azure Cloud Shell egy ingyenes interaktív felület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a cli-t a számítógépről futtatja, a 2.0.38-as vagy újabb verzióra lesz szüksége. A telepített verzió megkereséhez futtassa a következőt `az --version` a számítógépen: . Ha frissítenie kell, tekintse meg az [Azure CLI telepítését.](/cli/azure/install-azure-cli) Ha helyileg futtatja a CLI-t, akkor a futtatásával is be kell jelentkeznie az Azure-ba egy olyan fiókkal, amely rendelkezik a szükséges `az login` [engedélyekkel.](virtual-network-network-interface.md#permissions)

Az erőforrás-naplózás engedélyezéséhez szüksége lesz egy meglévő NSG azonosítójára. Ha még nem létezik NSG, létrehozhat egyet [az az network nsg create segítségével.](/cli/azure/network/nsg#az-network-nsg-create)

Az az network nsg show parancs segítségével olvassa be azt a hálózati biztonsági csoportot, amely számára engedélyezni szeretné az [erőforrás-naplózást.](/cli/azure/network/nsg#az-network-nsg-show) Ha például egy *myNsg* nevű NSG-t kell lekérni egy *myResourceGroup* nevű erőforráscsoportban, írja be a következő parancsot:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Az erőforrásnaplókat három céltípusba írhatja. További információ: [Naplócélok.](#log-destinations) Ebben a cikkben például naplókat küldjük el a Log Analytics-célhelyre.  További információ: [Naplókategóriák.](#log-categories)

Engedélyezze az NSG erőforrás-naplózását [az az monitor diagnostic-settings create parancs segítségével.](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) Az alábbi példa az esemény- és számlálókategória-adatokat is naplózza egy *meglévő, myWorkspace* nevű munkaterületen, amely egy *myWorkspaces* nevű erőforráscsoportban található, valamint a korábban lekért NSG azonosítóját:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Ha még nem található munkaterülete, létrehozhat egyet a Azure Portal [vagy](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [a PowerShell használatával.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) A naplózásnak két kategóriája van, amelyekhez engedélyezheti a naplókat.

Ha csak az egyik kategóriához vagy a másikhoz szeretne adatokat naplózni, távolítsa el azt a kategóriát, amely esetében nem szeretne adatokat naplózni az előző parancsban. Ha a Log Analytics-munkaterülettől eltérő célhelyre szeretne bejelentkezni, használja a megfelelő paramétereket egy Azure [Storage-fiókhoz](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) vagy [eseményközponthoz.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) [](#log-destinations)

Naplók megtekintése és elemzése. További információ: [Naplók megtekintése és elemzése.](#view-and-analyze-logs)

## <a name="log-destinations"></a>Naplócélok

Diagnosztikai adatok a következő lehet:
- [Egy Azure Storage-fiókba íródott](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)naplózáshoz vagy manuális vizsgálathoz. A megőrzési időt (napokban) erőforrás-diagnosztikai beállításokkal adhatja meg.
- [Streamelve egy eseményközpontba](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) egy külső szolgáltatás vagy egy egyéni elemzési megoldás, például a PowerBI által történő adatbehozás céljából.
- [A naplókba Azure Monitor íródott.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)

## <a name="log-categories"></a>Naplókategóriák

A JSON-formátumú adatok a következő naplózási kategóriákba vannak írva:

### <a name="event"></a>Esemény

Az eseménynapló a MAC-cím alapján információkat tartalmaz arról, hogy mely NSG-szabályok vonatkoznak a virtuális gépekre. A rendszer minden eseményhez a következő adatokat naplózza. A következő példában a rendszer egy 192.168.1.4 IP-címmel és 00-0D-3A-92-6A-7C MAC-címmel naplózza az adatokat egy virtuális géphez:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Szabályszámláló

A szabályszámláló naplója információkat tartalmaz az erőforrásokra alkalmazott egyes szabályokról. A következő példaadatokat a rendszer minden szabály alkalmazásakor naplózza. A következő példában a rendszer egy 192.168.1.4 IP-címmel és 00-0D-3A-92-6A-7C MAC-címmel naplózza az adatokat egy virtuális géphez:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> A rendszer nem naplózza a kommunikáció forrás IP-címét. Engedélyezheti azonban [az NSG-forgalom](../network-watcher/network-watcher-nsg-flow-logging-portal.md) naplózását egy NSG-hez, amely naplózza a szabályszámláló összes adatát, valamint a kommunikációt kezdeményező forrás IP-címet. A rendszer az NSG-folyamatnapló adatait egy Azure Storage-fiókba fogja írni. Az adatokat az Azure-szolgáltatások [forgalomelemzési](../network-watcher/traffic-analytics.md) funkcióját használva Network Watcher.

## <a name="view-and-analyze-logs"></a>Naplók megtekintése és elemzése

Az erőforrásnaplók adatainak megtekintésével kapcsolatos további információkért lásd: [Az Azure platform naplóinak áttekintése.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha diagnosztikai adatokat küld a következő célokra:
- **Azure Monitor naplók:** A továbbfejlesztett elemzésekhez használhatja a hálózati biztonsági csoport elemzési megoldását. [](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) A megoldás vizualizációkat biztosít az NSG-szabályokhoz, amelyek engedélyezik vagy megtagadják a virtuális gép hálózati adapterének MAC-címenkénti forgalmát.
- **Azure Storage-fiók:** Az adatok egy fájlban PT1H.jstárolóba. A következőt találja:
  - Eseménynapló a következő elérési úton: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - A szabályszámláló naplója a következő elérési úton található: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Következő lépések

- További információ a [tevékenységnaplózásról.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) A tevékenységnaplózás alapértelmezés szerint engedélyezve van az Azure-beli üzembehelyező modellel létrehozott NSG-k esetében. Annak megállapításához, hogy mely műveletek voltak befejezve az NSG-ken a tevékenységnaplóban, keresse meg a következő erőforrástípusokat tartalmazó bejegyzéseket:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Ha meg szeretne ismerkedni a diagnosztikai információk naplózásának mikéntjével, és hogy az egyes adatfolyamok forrás IP-címét is meg tudja-e jelenni, tekintse meg az [NSG-forgalom naplózását.](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
