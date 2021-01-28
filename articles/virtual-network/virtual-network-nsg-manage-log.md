---
title: Hálózati biztonsági csoport diagnosztikai erőforrás-naplózása
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan engedélyezheti az események és a szabályok számlálójának diagnosztikai erőforrás-naplóit egy Azure hálózati biztonsági csoport számára.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 412556f3bd517539fc8ccad94c4de52226f16597
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946222"
---
# <a name="resource-logging-for-a-network-security-group"></a>Hálózati biztonsági csoport erőforrás-naplózása

A hálózati biztonsági csoport (NSG) olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózati alhálózatra, hálózati adapterre vagy mindkettőre. 

Amikor engedélyezi a naplózást egy NSG, a következő típusú erőforrás-naplózási információkat gyűjthet:

* **Esemény:** A rendszer naplózza azokat a bejegyzéseket, amelyek NSG-szabályait a rendszer MAC-címe alapján alkalmazza a virtuális gépekre.
* **Szabály számlálója:** Azokat a bejegyzéseket tartalmazza, amelyekkel az egyes NSG-szabályok a forgalom megtagadására vagy engedélyezésére vonatkoznak. A szabályok állapotát 300 másodpercenként gyűjti a rendszer.

Az erőforrás-naplók csak a Azure Resource Manager üzemi modellen keresztül üzembe helyezett NSG érhetők el. A klasszikus üzemi modellen keresztül üzembe helyezett NSG erőforrás-naplózása nem engedélyezhető. A két modell jobb megismeréséhez tekintse meg az [Azure üzembe helyezési modelljeinek ismertetése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.

Az erőforrás-naplózás *minden* olyan NSG külön engedélyezve van, amelyhez diagnosztikai adatokat kíván gyűjteni. Ha inkább a tevékenység (operatív) naplói érdeklik, tekintse meg az Azure- [tevékenységek naplózása](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.

## <a name="enable-logging"></a>Naplózás engedélyezése

Az erőforrás-naplózás engedélyezéséhez használhatja az [Azure Portalt](#azure-portal), a [PowerShellt](#powershell)vagy az [Azure CLI](#azure-cli) -t is.

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [portálra](https://portal.azure.com).
2. Válassza **a minden szolgáltatás** lehetőséget, majd írja be a *hálózati biztonsági csoportok* elemet. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
3. Válassza ki azt a NSG, amely számára engedélyezni kívánja a naplózást.
4. A **figyelés** területen válassza a **diagnosztikai naplók** lehetőséget, majd kattintson a **diagnosztika bekapcsolása** elemre, ahogy az a következő képen látható:

   ![Diagnosztika bekapcsolása](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. A **diagnosztika beállításai** alatt adja meg a következő információkat, vagy válassza ki az alábbi adatokat, majd kattintson a **Mentés** gombra:

    | Beállítás                                                                                     | Érték                                                          |
    | ---------                                                                                   |---------                                                       |
    | Név                                                                                        | A választott név.  Például: *myNsgDiagnostics*      |
    | **Archiválás egy Storage-fiókba**, **adatfolyam küldése az Event hub**-nak, és **Küldés log Analytics** | Tetszőleges számú célhelyet választhat ki. Ha többet szeretne megtudni az egyes szolgáltatásokról, tekintse meg a [naplók célhelyeit](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Válassza ki vagy mindkét naplózási kategóriát. Ha többet szeretne megtudni az egyes kategóriákba bejelentkezett információkról, tekintse meg a [naplózási kategóriákat](#log-categories).                                                                                                                                             |
6. Naplók megtekintése és elemzése. További információt a [naplók megtekintése és elemzése](#view-and-analyze-logs)című témakörben talál.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0 vagy újabb verzióra. Futtassa a parancsot a `Get-Module -ListAvailable Az` számítógépen, és keresse meg a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor az Azure-ba való bejelentkezéshez is futtatnia kell `Connect-AzAccount` egy olyan fiókkal, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).

Az erőforrás-naplózás engedélyezéséhez egy meglévő NSG azonosítóra van szükség. Ha nem rendelkezik meglévő NSG, létrehozhat egyet a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)használatával.

Kérje le azt a hálózati biztonsági csoportot, amelyhez engedélyezni szeretné az erőforrás-naplózást a [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)használatával. Ha például egy *myNsg* nevű NSG szeretne beolvasni egy *myResourceGroup* nevű erőforráscsoporthoz, írja be a következő parancsot:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Az erőforrás-naplókat három célhelyre írhatja. További információ: [naplók célhelyei](#log-destinations). Ebben a cikkben a naplók a *log Analytics* célhelyre kerülnek, példaként. Meglévő Log Analytics-munkaterület beolvasása a [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Ha például egy *sajátmunkaterület* nevű meglévő munkaterületet szeretne beolvasni egy *myWorkspaces* nevű erőforráscsoporthoz, írja be a következő parancsot:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Ha nem rendelkezik meglévő munkaterülettel, létrehozhat egyet a [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)használatával.

A naplózásnak két kategóriája is engedélyezhető. További információ: [naplózási kategóriák](#log-categories). Engedélyezze a NSG erőforrás-naplózását a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Az alábbi példa az esemény-és a számlálók NSG a munkaterületre naplózza a korábban lekért NSG és munkaterülethez tartozó azonosítók használatával:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Ha a kettő helyett egyetlen kategóriához vagy a másikhoz kívánja naplózni az adatnaplózást, adja hozzá az `-Categories` előző parancshoz a kapcsolót, amelyet a *NetworkSecurityGroupEvent* vagy a *NetworkSecurityGroupRuleCounter* követ. Ha Log Analytics munkaterülettől eltérő [célra](#log-destinations) szeretne bejelentkezni, használja az Azure [Storage-fiók](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) vagy az [Event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)megfelelő paramétereit.

Naplók megtekintése és elemzése. További információt a [naplók megtekintése és elemzése](#view-and-analyze-logs)című témakörben talál.

### <a name="azure-cli"></a>Azure CLI

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/bash), vagy futtathatja az Azure CLI-t a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a parancssori felületet a számítógépről futtatja, akkor a 2.0.38 vagy újabb verzióra van szükség. Futtassa a parancsot a `az --version` számítógépen, és keresse meg a telepített verziót. Ha frissítenie kell, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. Ha helyileg futtatja a CLI-t, akkor az Azure-ba való bejelentkezéshez is futtatnia kell `az login` egy olyan fiókkal, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).

Az erőforrás-naplózás engedélyezéséhez egy meglévő NSG azonosítóra van szükség. Ha nem rendelkezik meglévő NSG, létrehozhat egyet az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal.

Kérje le azt a hálózati biztonsági csoportot, amelyhez engedélyezni szeretné az erőforrás-naplózást az az [Network NSG show](/cli/azure/network/nsg#az-network-nsg-show)paranccsal. Ha például egy *myNsg* nevű NSG szeretne beolvasni egy *myResourceGroup* nevű erőforráscsoporthoz, írja be a következő parancsot:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Az erőforrás-naplókat három célhelyre írhatja. További információ: [naplók célhelyei](#log-destinations). Ebben a cikkben a naplók a *log Analytics* célhelyre kerülnek, példaként. További információ: [naplózási kategóriák](#log-categories).

Engedélyezze az erőforrás-naplózást a NSG az [az monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create)paranccsal. A következő példa a *sajátmunkaterület* nevű meglévő munkaterületre, az *myWorkspaces* nevű erőforráscsoport és a korábban BEolvasott NSG azonosítójának az eseményeit és a számlálók kategóriáját is naplózza:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Ha nem rendelkezik meglévő munkaterülettel, létrehozhat egyet a [Azure Portal](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)használatával. A naplózásnak két kategóriája is engedélyezhető.

Ha csak az egyik kategóriába vagy a másikba kívánja naplózni az adatnaplózást, távolítsa el azt a kategóriát, amelyet nem kíván naplózni az előző parancsban. Ha Log Analytics munkaterülettől eltérő [célra](#log-destinations) szeretne bejelentkezni, használja az Azure [Storage-fiók](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) vagy az [Event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)megfelelő paramétereit.

Naplók megtekintése és elemzése. További információt a [naplók megtekintése és elemzése](#view-and-analyze-logs)című témakörben talál.

## <a name="log-destinations"></a>Naplók célhelyei

A diagnosztikai adatait a következőket teheti:
- [Egy Azure Storage-fiókba](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage), naplózásra vagy manuális ellenőrzésre íródott. Megadhatja a megőrzési időt (napokban) az erőforrás-diagnosztikai beállítások használatával.
- Egy harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás (például PowerBI) általi betöltésre [továbbítva egy Event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) számára.
- [Azure monitor naplókba írva](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Naplók kategóriái

A JSON formátumú adat a következő naplózási kategóriákba van írva:

### <a name="event"></a>Esemény

Az Eseménynapló információkat tartalmaz arról, hogy mely NSG szabályok vonatkoznak a virtuális gépekre a MAC-címek alapján. A rendszer minden eseménynél naplózza a következő adatgyűjtést. Az alábbi példában az IP-192.168.1.4 és a 00-0D-3A-92-6A-7C MAC-címmel rendelkező virtuális gép esetében a rendszer naplózza az adatmennyiséget.

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

### <a name="rule-counter"></a>Szabály számlálója

A szabály számlálójának naplója az erőforrásokra alkalmazott szabályokról tartalmaz információkat. A rendszer minden alkalommal naplózza a következő példákat, amikor egy szabály alkalmazva lesz. Az alábbi példában az IP-192.168.1.4 és a 00-0D-3A-92-6A-7C MAC-címmel rendelkező virtuális gép esetében a rendszer naplózza az adatmennyiséget.

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
> A kommunikáció forrás IP-címe nincs naplózva. Engedélyezheti a [NSG folyamat naplózását](../network-watcher/network-watcher-nsg-flow-logging-portal.md) egy NSG, amely naplózza az összes szabály számlálójának adatait, valamint a kommunikációt kezdeményező forrás IP-címet is. A rendszer az NSG-folyamatnapló adatait egy Azure Storage-fiókba fogja írni. Az adatokat elemezheti az Azure Network Watcher [Traffic Analytics](../network-watcher/traffic-analytics.md) -képességével.

## <a name="view-and-analyze-logs"></a>Naplók megtekintése és elemzése

Az erőforrás-naplózási információk megtekintésének megismeréséhez lásd: az [Azure platform naplói – áttekintés](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha diagnosztikai adatait küldi el a következőnek:
- **Azure monitor naplók**: a [hálózati biztonsági csoport elemzési](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) megoldásával továbbfejlesztett elemzéseket készíthet. A megoldás olyan vizualizációkat biztosít a NSG-szabályokhoz, amelyek engedélyezik vagy megtagadják a virtuális gép hálózati adapterének forgalmát, MAC-címként.
- **Azure Storage-fiók**: az adatfájlok egy PT1H.jsba íródnak. A következőket találja:
  - Eseménynapló a következő elérési úton: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Szabály számlálójának naplója a következő elérési úton: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Következő lépések

- További információ a [tevékenységek naplózásáról](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A tevékenységek naplózása alapértelmezés szerint engedélyezve van az Azure-alapú üzemi modellel létrehozott NSG. Annak megállapításához, hogy mely műveletek befejeződtek a NSG a tevékenység naplójában, keresse meg a következő típusú erőforrásokat tartalmazó bejegyzéseket:
  - Microsoft. ClassicNetwork/networkSecurityGroups
  - Microsoft. ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft. Network/networkSecurityGroups
  - Microsoft. Network/networkSecurityGroups/securityRules
- A diagnosztikai adatok naplózásának megismeréséhez az egyes folyamatok forrás IP-címének belefoglalásához lásd: [NSG flow naplózása](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
