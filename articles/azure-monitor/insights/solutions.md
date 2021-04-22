---
title: Monitorozási megoldások a Azure Monitor | Microsoft Docs
description: A monitorozási megoldások Azure Monitor logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problématerületre vonatkozó metrikákat biztosítanak.  Ez a cikk a monitorozási megoldások telepítésével és használatával kapcsolatos információkat tartalmaz.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 6a98571f513e25d801acd4f4a9c2901dcd56fabc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869220"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Megoldások monitorozása a Azure Monitor

A monitorozási Azure Monitor egy adott Azure-alkalmazás vagy -szolgáltatás működésének elemzését biztosítják. Ez a cikk rövid áttekintést nyújt az Azure-beli monitorozási megoldásokról, valamint a használatukról és telepítésükről. A használt alkalmazásokhoz és szolgáltatásokhoz Azure Monitor hozzáadhat figyelési megoldásokat. Ezek általában költség nélkül érhetők el, de használati díjakat meghívó adatokat gyűjtenek.

## <a name="use-monitoring-solutions"></a>Monitorozási megoldások használata

A megoldások **Áttekintés** Azure Monitor log Analytics-munkaterületen telepített összes megoldáshoz megjelenít egy csempét. Az oldal megnyitásához nyissa meg a **Azure Monitor** a [Azure Portal.](https://ms.portal.azure.com) Az **Elemzések menüben** válassza **a** További lehetőséget az **Insights-központ megnyitásához,** majd kattintson a **Log Analytics-munkaterületek elemre.**

[![Insights Hub](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


A képernyő tetején található legördülő mezőivel módosíthatja a munkaterületet vagy a csempékhez használt időtartományt. A megoldás csempéjére kattintva megnyithatja azt a nézetet, amely részletesebb elemzést tartalmaz az összegyűjtött adatokról.

[![Képernyőkép a Azure Portal menüről, a megoldások kiválasztva, a megoldások pedig a Megoldások panelen jelennek meg.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

A monitorozási megoldások több típusú Azure-erőforrást is tartalmazhatnak, és a megoldáshoz mellékelt erőforrások bármelyikét megtekintheti, akárcsak bármely más erőforrás. Például a megoldásban szereplő összes naplólekérdezés megjelenik a **Megoldáslekérdezések a** [Lekérdezéskezelőben listában.](../logs/log-analytics-tutorial.md) Ezeket a lekérdezéseket akkor használhatja, ha ad hoc elemzést végez a [naplólekérdezésekkel.](../logs/log-query-overview.md)

## <a name="list-installed-monitoring-solutions"></a>Telepített monitorozási megoldások listása

### <a name="portal"></a>[Portál](#tab/portal)

Az alábbi eljárással listába sorolja fel az előfizetésében telepített monitorozási megoldásokat.

1. Nyissa meg az [Azure Portal](https://ms.portal.azure.com). Keresse meg és válassza a **Megoldások lehetőséget.**
1. Megjelenik az összes munkaterületen telepített megoldások listája. A megoldás nevét a telepített munkaterület neve követi.
1. A képernyő tetején található legördülő mezőivel előfizetés vagy erőforráscsoport szerint szűrhet.

![Az összes megoldás listása](media/solutions/list-solutions-all.png)

Kattintson egy megoldás nevére az összefoglaló oldal megnyitásához. Ezen az oldalon megjelenik a megoldásban található összes nézet, és különböző lehetőségeket biztosít magának a megoldásnak és annak munkaterületének. Tekintse meg egy megoldás összegző lapját a fenti eljárások egyikével a megoldások listához, majd kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az monitor log-analytics solution list paranccsal](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) listába sorolhatja az előfizetésében telepített monitorozási megoldásokat.   A parancs futtatása előtt kövesse a Következő cikk előfeltételeit: `list` [Monitorozási megoldás telepítése.](#install-a-monitoring-solution)

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

A [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) parancsmag használatával listázhatóak az előfizetésben telepített monitorozási megoldások. A parancsok futtatása előtt kövesse a Következő cikk előfeltételeit: [Monitorozási megoldás telepítése.](#install-a-monitoring-solution)

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Monitorozási megoldás telepítése

### <a name="portal"></a>[Portál](#tab/portal)

A Microsoft és partnerei által biztosított monitorozási megoldások a [Azure Marketplace.](https://azuremarketplace.microsoft.com) A következő eljárással kereshet az elérhető megoldások között, és telepítheti őket. Megoldás telepítésekor ki kell választania egy Log Analytics-munkaterületet, ahol a megoldás telepítve lesz, és ahol az adatok gyűjtése történik. [](../logs/manage-access.md)

1. Az előfizetés [megoldási listájában kattintson az](#list-installed-monitoring-solutions) **Add (Hozzáadás) gombra.**
1. Megoldás tallózása vagy keresése. A megoldásokat ezen a keresési [hivatkozáson is böngészheti.](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)
1. Keresse meg a kívánt monitorozási megoldást, és olvassa el a leírását.
1. Kattintson **a Létrehozás** gombra a telepítési folyamat elkezdéséhez.
1. A telepítési folyamat indításakor a rendszer felkéri, hogy adja meg a Log Analytics-munkaterületet, és adja meg a megoldáshoz szükséges konfigurációkat.

![Megoldás telepítése](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Megoldás telepítése a közösségtől

A közösség tagjai beküldhet felügyeleti megoldásokat az Azure gyorsindítási sablonokba. Ezeket a megoldásokat telepítheti közvetlenül, vagy letöltheti őket a későbbi telepítéshez.

1. Kövesse a [Log Analytics-munkaterület](#log-analytics-workspace-and-automation-account) és az Automation-fiók leírásában leírt folyamatot egy munkaterület és egy fiók csatolásához.
2. Ugrás az [Azure gyorsindítási sablonokra.](https://azure.microsoft.com/documentation/templates/)
3. Keressen egy olyan megoldást, amely érdekli.
4. Válassza ki a megoldást az eredmények közül a részletek megtekintéséhez.
5. Kattintson az Üzembe **helyezés az Azure-ban gombra.**
6. A rendszer arra kéri, hogy adjon meg olyan információkat, mint az erőforráscsoport és a hely, valamint a megoldásban található paraméterek értékei.
7. A **megoldás telepítéséhez** kattintson a Vásárlás gombra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>A környezet előkészítése

1. Az Azure CLI összetevő telepítése

   A [CLI-referenciaparancsok](/cli/azure/install-azure-cli) futtatása előtt telepítenie kell az Azure CLI-t.  Ha szeretné, a Azure Cloud Shell is használhatja.  Azure Cloud Shell egy interaktív felületi környezet, amely a böngészőben használható.  Kezdje Cloud Shell a következő módszerek egyikével:

   - Nyissa Cloud Shell következőt: [https://shell.azure.com](https://shell.azure.com)

   - Kattintson **a Cloud Shell** jobb felső sarkában található menüsáv [](https://portal.azure.com) Azure Portal

1. Jelentkezzen be.

   Ha a CLI helyi telepítését használja, jelentkezzen be az [az login paranccsal.](/cli/azure/reference-index#az_login)  A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

    ```azurecli
    az login
    ```

1. A bővítmény `log-analytics-solution` telepítése

   A `log-analytics-solution` parancs az alapvető Azure CLI kísérleti bővítménye. További információ a bővítményhivatkozásokkal [kapcsolatban: Bővítmény használata az Azure CLI-val.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   A következő figyelmeztetés várható.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Megoldás telepítése az Azure CLI használatával

Megoldás telepítésekor ki kell választania egy Log Analytics-munkaterületet, ahol a megoldás telepítve lesz, és ahol az adatok gyűjtése történik. [](../logs/manage-access.md)  Az Azure CLI használatával az az [monitor log-analytics workspace reference commands (az monitor log-analytics workspace reference](/cli/azure/monitor/log-analytics/workspace) commands) parancsokkal kezelheti a munkaterületeket.  Kövesse a [Log Analytics-munkaterület](#log-analytics-workspace-and-automation-account) és az Automation-fiók leírásában leírt folyamatot egy munkaterület és egy fiók csatolásához.

Monitorozási [megoldás telepítéséhez használja az az monitor log-analytics create](/cli/azure/monitor/log-analytics/solution) megoldást.  A szögletes zárójelben lévő paraméterek megadása nem kötelező.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Itt található egy kódminta, amely létrehoz egy log analytics-megoldást az OMSGallery/Containers csomagtermékhez.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>A környezet előkészítése

1. Az Azure PowerShell telepítése

   A [referenciaparancsok Azure PowerShell](/powershell/azure/install-az-ps) előtt telepítenie kell Azure PowerShell Azure PowerShell parancsot. Ha szeretné, a Azure Cloud Shell is használhatja. Azure Cloud Shell egy interaktív felületi környezet, amely a böngészőben használható. Kezdje Cloud Shell a következő módszerek egyikével:

   - Nyissa Cloud Shell következőt: [https://shell.azure.com](https://shell.azure.com)

   - Kattintson **a Cloud Shell** jobb felső sarkában található menüsáv [](https://portal.azure.com) Azure Portal

   > [!IMPORTANT]
   > Bár az **Az.MonitoringSolutions** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Jelentkezzen be.

   Ha a PowerShell helyi telepítését használja, jelentkezzen be a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. A hitelesítési folyamat befejezéséhez kövesse a PowerShellben megjelenő lépéseket.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Megoldás telepítése a Azure PowerShell

Megoldás telepítésekor ki kell választania egy Log Analytics-munkaterületet, ahol a megoldás telepítve lesz, és ahol az adatok gyűjtése történik. [](../logs/manage-access.md) A Azure PowerShell az [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell-modul parancsmagokkal kezelheti a munkaterületeket. Kövesse a [Log Analytics-munkaterület](#log-analytics-workspace-and-automation-account) és az Automation-fiók leírásában leírt folyamatot egy munkaterület és egy fiók csatolásához.

Monitorozási megoldás telepítéséhez használja a [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) parancsmagot. A szögletes zárójelben lévő paraméterek megadása nem kötelező.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

Az alábbi példa egy monitornapló-elemzési megoldást hoz létre a Log Analytics-munkaterülethez.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-munkaterület és Automation-fiók

Minden monitorozási megoldáshoz szükség van egy [Log Analytics-munkaterületre](../logs/manage-access.md) a megoldás által gyűjtött adatok tárolására, valamint a naplókeresések és -nézetek tárolására. Egyes megoldásokhoz [automation-fiókra is szükség van](../../automation/automation-security-overview.md) a runbookok és a kapcsolódó erőforrások tartalmazása érdekében. A munkaterületnek és a fióknak az alábbi követelményeknek kell megfelelnie.

* Egy megoldás minden egyes telepítése csak egy Log Analytics-munkaterületet és egy Automation-fiókot használhat. A megoldást külön is telepítheti több munkaterületre.
* Ha egy megoldáshoz Automation-fiókra van szükség, akkor a Log Analytics-munkaterületet és az Automation-fiókot össze kell kapcsolni egymással. Egy Log Analytics-munkaterület csak egy Automation-fiókhoz, egy Automation-fiók pedig csak egy Log Analytics-munkaterülethez lehet csatolva.

Amikor egy megoldást telepít a Azure Marketplace, a rendszer kéri a munkaterület és az Automation-fiók kérését. A közöttük található kapcsolat akkor jön létre, ha még nincsenek összekapcsolva.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics-munkaterület és Automation-fiók közötti kapcsolat ellenőrzése

A Log Analytics-munkaterület és az Automation-fiók közötti kapcsolatot az alábbi eljárással ellenőrizheti.

1. Válassza ki az Automation-fiókot a Azure Portal.
1. Görgessen **a menü Kapcsolódó erőforrások** szakaszára, és válassza a Csatolt munkaterület **lehetőséget.**
1. Ha **a munkaterület** egy Automation-fiókhoz van kapcsolva, akkor ez az oldal felsorolja a munkaterületet, amelyhez kapcsolódik. Ha kiválasztja a listában szereplő munkaterület nevét, a rendszer átirányítja a munkaterület áttekintési oldalára.

## <a name="remove-a-monitoring-solution"></a>Monitorozási megoldás eltávolítása

### <a name="portal"></a>[Portál](#tab/portal)

Ha el szeretne távolítani egy telepített megoldást a portál használatával, keresse meg a telepített [megoldások listájában.](#list-installed-monitoring-solutions) Kattintson a megoldás nevére az összefoglaló oldal megnyitásához, majd kattintson a **Törlés elemre.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával szeretne eltávolítani egy telepített megoldást, használja [az az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete) parancsot.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Ha egy telepített megoldást szeretne eltávolítani a Azure PowerShell, használja a [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) parancsmagot.

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Következő lépések

* A [microsoftos monitorozási megoldások listájának lekért listája.](../monitor-reference.md)
* Megtudhatja, hogyan [hozhat létre lekérdezéseket](../logs/log-query-overview.md) a monitorozási megoldások által gyűjtött adatok elemzéséhez.
* Tekintse meg az [összes Azure CLI-parancsot a Azure Monitor.](/cli/azure/azure-cli-reference-for-monitor)