---
title: Figyelési Azure Queue Storage
description: Megtudhatja, hogyan figyelheti a teljesítmény és a rendelkezésre állás Azure Queue Storage. Figyelheti Azure Queue Storage adatait, megismerheti a konfigurációt, és elemezheti a metrika- és naplóadatokat.
author: normesta
services: storage
ms.author: normesta
ms.reviewer: fryu
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b65aff45cc304f59e45fc3bed925b93ee6c622fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788402"
---
# <a name="monitoring-azure-queue-storage"></a>Figyelési Azure Queue Storage

Ha kritikus fontosságú, Azure-erőforrásokra támaszkodó alkalmazásokkal és üzleti folyamatokkal rendelkezik, ezeket az erőforrásokat figyelni szeretné a rendelkezésre állásuk, a teljesítményük és működésük szempontjából. Ez a cikk a Azure Queue Storage által létrehozott monitorozási adatokat ismerteti, és hogy a Azure Monitor funkciói hogyan elemezhetők az adatokra vonatkozó riasztások.

> [!NOTE]
> Az Azure Storage-Azure Monitor nyilvános előzetes verzióban érhető el, és előzetes verzióban tesztelhető minden nyilvános felhőrégióban. Ez az előzetes verzió lehetővé teszi a blobok (például Azure Data Lake Storage Gen2), fájlok, üzenetsorok és táblák naplóit. Ez a szolgáltatás minden olyan tárfiókhoz elérhető, amely a Azure Resource Manager modellel jött létre. Lásd: [Tárfiók áttekintése.](../common/storage-account-overview.md)

## <a name="monitor-overview"></a>Figyelés áttekintése

Az **egyes** erőforrásokhoz Azure Portal áttekintési Queue Storage áttekintést nyújt az erőforrás-használatról, például a kérelmekről és az óránkénti számlázásról. Ezek az információk hasznosak, de csak kis mennyiségű monitorozási adat áll rendelkezésre. Ezen adatok egy része automatikusan gyűjthető, és az erőforrás létrehozása után már elérhető elemzésre. Bizonyos konfigurációval további adatgyűjtési típusokat is engedélyezhet.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

Azure Queue Storage adatokat hoz létre a [Azure Monitor](../../azure-monitor/overview.md)használatával, amely egy teljes körű monitorozási szolgáltatás az Azure-ban. Azure Monitor azure-erőforrások, valamint más felhőkben és a helyszínen található erőforrások figyelése teljes körű funkciókat biztosít.

Kezdje az [Azure-erőforrások figyelése Azure Monitor,](../../azure-monitor/essentials/monitor-azure-resource.md) amely a következőket ismerteti:

- Mi az Azure Monitor?
- A monitorozással kapcsolatos költségek
- Az Azure-ban gyűjtött monitorozási adatok
- Adatgyűjtés konfigurálása
- Standard eszközök az Azure-ban az adatok monitorozásának elemzéséhez és riasztáshoz

A következő szakaszok erre a cikkre épülnek az Azure Storage-ból gyűjtött adatok leírásán keresztül. A példák bemutatják, hogyan konfigurálhatja az adatgyűjtést, és hogyan elemezheti ezeket az adatokat Azure-eszközökkel.

## <a name="monitoring-data"></a>Adatok monitorozása

Azure Queue Storage gyűjti a monitorozási adatokat, mint a többi Azure-erőforrás, amelyeket az Azure-erőforrásokból származó adatok monitorozása [leírás ismertet.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)

A [Azure Queue Storage által](monitor-queue-storage-reference.md) létrehozott metrikákra és naplókra vonatkozó részletes információkért tekintse meg a Azure Queue Storage.

A tárfiókok metrika- Azure Monitor naplói csak Azure Resource Manager tárfiókokat támogatnak. Azure Monitor nem támogatja a klasszikus tárfiókokat. Ha klasszikus tárfiókban szeretne metrikákat vagy naplókat használni, mihelyt egy Azure Resource Manager tárfiókba. Lásd: [Áttelepítés Azure Resource Manager.](../../virtual-machines/migration-classic-resource-manager-overview.md)

Ha szeretné, továbbra is használhatja a klasszikus metrikákat és naplókat. Valójában a klasszikus metrikák és naplók párhuzamosan érhetők el a metrikák és naplók Azure Monitor. A támogatás mindaddig érvényben marad, amíg az Azure Storage be nem befejezi a szolgáltatást a régi metrikák és naplók alapján.

## <a name="collection-and-routing"></a>Gyűjtés és útválasztás

A platformmetrikák és a tevékenységnaplók gyűjtése automatikusan történik, de diagnosztikai beállítással más helyekre is irányíthatóak.

Az erőforrásnaplók gyűjtéséhez létre kell hoznia egy diagnosztikai beállítást. A beállítás létrehozásakor válassza  az üzenetsort az a tárolótípus, amely számára engedélyezni szeretné a naplókat. Ezután adja meg a következő műveletkategóriák egyikét, amelyekhez naplókat szeretne gyűjteni.

| Kategória | Leírás |
|:---|:---|
| **StorageRead** | Olvasási műveletek objektumokon. |
| **StorageWrite** | Írási műveletek objektumokon. |
| **StorageDelete** | Objektumokon lévő műveletek törlése. |

## <a name="creating-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

Diagnosztikai beállítást a következő sablonnal hozhat létre: Azure Portal, a PowerShell, az Azure CLI vagy Azure Resource Manager sablon.

Általános útmutatásért lásd: Diagnosztikai beállítás [létrehozása platformnaplók és -metrikák gyűjtéséhez az Azure-ban.](../../azure-monitor/essentials/diagnostic-settings.md)

> [!NOTE]
> Az Azure Storage Azure Monitor naplók nyilvános előzetes verzióban érhetők el, és előzetes verzióban tesztelhetőek az összes nyilvános felhőrégióban. Ez az előzetes verzió engedélyezi a blobok (beleértve a Azure Data Lake Storage Gen2), a fájlok, az üzenetsorok és a táblák naplóit. Ez a szolgáltatás az üzembe helyezési modellel létrehozott összes tárfiókhoz Azure Resource Manager érhető el. Lásd: [Tárfiók áttekintése.](../common/storage-account-overview.md)

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure Portalra.

2. Nyissa meg a tárfiókot.

3. A **Figyelés szakaszban** kattintson a Diagnosztikai **beállítások (előzetes verzió) elemre.**

   > [!div class="mx-imgBorder"]
   > ![portál – Diagnosztikai naplók](media/monitor-queue-storage/diagnostic-logs-settings-pane.png)

4. Válassza **ki az** üzenetsort az a tárolótípus, amely számára engedélyezni szeretné a naplókat.

5. Kattintson **a Diagnosztikai beállítás hozzáadása elemre.**

   > [!div class="mx-imgBorder"]
   > ![portál – Erőforrásnaplók – diagnosztikai beállítás hozzáadása](media/monitor-queue-storage/diagnostic-logs-settings-pane-2.png)

   Megjelenik a Diagnosztikai **beállítások** lap.

   > [!div class="mx-imgBorder"]
   > ![Erőforrásnaplók oldal](media/monitor-queue-storage/diagnostic-logs-page.png)

6. Az oldal **Név** mezőjében adja meg az erőforrásnapló-beállítás nevét. Ezután válassza ki, hogy mely műveleteket naplózza (olvasási, írási és törlési műveletek), és hová szeretné küldeni a naplókat.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása tárfiókba

Ha úgy dönt, hogy a naplókat egy tárfiókba archiválja, akkor a tárfiókba küldött naplók mennyisége után fizet. Az egyes díjszabásokért tekintse **meg** a Azure Monitor a Platformnaplók [című szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Jelölje be **az Archiválás tárfiókba** jelölőnégyzetet, majd kattintson a **Konfigurálás gombra.**

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai beállítások laparchiválási tárhelye](media/monitor-queue-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. A **Tárfiók legördülő** listában válassza ki azt a tárfiókot, amelybe a naplókat archiválni szeretné, kattintson az **OK** gombra, majd válassza a **Mentés** gombot.
 
   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Mielőtt kiválaszt egy tárfiókot exportálási [](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) célként, tekintse meg az Azure-erőforrásnaplók archiválását a tárfiók előfeltételeinek megtekintéséhez.

#### <a name="stream-logs-to-azure-event-hubs"></a>Naplók streamelése Azure Event Hubs

Ha úgy dönt, hogy a naplókat egy eseményközpontba streameli, fizetnie kell az eseményközpontba küldött naplók mennyiségért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor a Platformnaplók [című szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Jelölje be **a Streamelés eseményközpontba** jelölőnégyzetet, majd kattintson a **Konfigurálás** gombra.

2. Az **Eseményközpont** kiválasztása panelen válassza ki annak az eseményközpontnak a névterét, nevét és szabályzatnevét, amelybe a naplókat streamelni szeretné.

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai beállítások lap eseményközpontja](media/monitor-queue-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Kattintson az **OK** gombra, majd válassza a **Mentés** gombot.

#### <a name="send-logs-to-azure-log-analytics"></a>Naplók küldése az Azure Log Analyticsbe

1. Jelölje be **a Küldés a Log Analyticsbe** jelölőnégyzetet, válasszon ki egy Log Analytics-munkaterületet, majd kattintson a Mentés **gombra.**

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai beállítások lap naplóelemzése](media/monitor-queue-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nyisson Windows PowerShell egy parancssori ablakot, és jelentkezzen be az Azure-előfizetésbe az `Connect-AzAccount` paranccsal. Ezután kövesse a képernyőn megjelenő utasításokat.

   ```powershell
   Connect-AzAccount
   ```

2. Állítsa be az aktív előfizetést annak a tárfióknak az előfizetéséhez, amely számára engedélyezni szeretné a naplózást.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása tárfiókba

Ha a naplókat egy tárfiókba archiválja, fizetnie kell a tárfiókba küldött naplókért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat a [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-parancsmag és a paraméter `StorageAccountId` használatával.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log>
```

Cserélje le az ebben a kódrészletben lévő `<storage-service-resource--id>` helyőrzőt az üzenetsor erőforrás-azonosítójára. Az erőforrás-azonosítót a Azure Portal tárfiók **Tulajdonságok** lapján találja.

A Category paraméter `StorageRead` értéke `StorageWrite` , és `StorageDelete` lehet. 

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Az egyes paraméterek leírását lásd: [Azure-erőforrásnaplók archiválása a Azure PowerShell.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Naplók streamelése egy eseményközpontba

Ha úgy dönt, hogy a naplókat egy eseményközpontba streameli, fizetnie kell az eseményközpontba küldött naplók mennyiségért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat a [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-parancsmag és a paraméter `EventHubAuthorizationRuleId` használatával.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Az egyes paraméterek leírását lásd: Streamelési adatok Event Hubs [PowerShell-parancsmagokkal.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat a [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-parancsmag és a paraméter `WorkspaceId` használatával.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

További információ: [Azure-erőforrásnaplók streamelése a Log Analytics-munkaterületre a Azure Monitor.](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Először nyissa meg a [Azure Cloud Shell,](../../cloud-shell/overview.md)vagy ha helyileg telepítette az [Azure CLI-t,](/cli/azure/install-azure-cli) nyisson meg egy parancskonzol-alkalmazást, például a PowerShellt.

2. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést annak a tárfióknak az előfizetéséhez, amely számára engedélyezni szeretné a naplókat.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása tárfiókba

Ha a naplókat egy tárfiókba archiválja, fizetnie kell a tárfiókba küldött naplókért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat az [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Cserélje le az ebben a kódrészletben lévő `<storage-service-resource--id>` helyőrzőt az üzenetsor erőforrás-azonosítójára. Az erőforrás-azonosítót a Azure Portal tárfiók **Tulajdonságok** lapján találja.

A paraméter értéke `StorageRead` , `StorageWrite` és `StorageDelete` `category` lehet.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Az egyes paraméterek leírását lásd: [Erőforrásnaplók archiválása az Azure CLI-n keresztül.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Naplók streamelése egy eseményközpontba

Ha úgy dönt, hogy a naplókat egy eseményközpontba streameli, fizetnie kell az eseményközpontba küldött naplók mennyiségért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat az [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Az egyes paraméterek leírását lásd: Streamelési adatok Event Hubs [Azure CLI-n keresztül.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat az [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 További információ: [Azure-erőforrásnaplók streamelése a Log Analytics-munkaterületre a Azure Monitor.](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

# <a name="template"></a>[Sablon](#tab/template)

Diagnosztikai beállítást Azure Resource Manager sablon megtekintéséhez tekintse meg az Azure Storage diagnosztikai [beállítását.](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)

---

## <a name="analyzing-metrics"></a>Metrikák elemzése

Az Azure Storage más Azure-szolgáltatások metrikai alapján elemezheti az Azure Storage metrikákat az Azure Metrikaböngésző. Nyissa Metrikaböngésző a **Metrikák lehetőséget** a Azure Monitor **menüből.** Az eszköz használatával kapcsolatos részletekért lásd: Ismerkedés az [Azure Metrikaböngésző.](../../azure-monitor/essentials/metrics-getting-started.md)

Ez a példa bemutatja, hogyan lehet megtekinteni **a** tranzakciókat a fiók szintjén.

![Képernyőkép a metrikák eléréséhez a Azure Portal](./media/monitor-queue-storage/access-metrics-portal.png)

A dimenziókat támogató metrikák esetén a kívánt dimenzióértékkel szűrheti a metrikát. Ez a példa bemutatja, hogyan **lehet** megtekinteni a tranzakciókat egy adott művelet fiókszinten az API Name dimenzió **értékeinek kiválasztásával.**

![Képernyőkép a metrikák eléréséhez a dimenzióval a Azure Portal](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [Metrikadimenziók.](monitor-queue-storage-reference.md#metrics-dimensions)

A Azure Queue Storage a következő névterek metrikai:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/queueServices

A támogatott metrikákat Azure Monitor(et) Azure Queue Storage összes [Azure Monitor.](../../azure-monitor/essentials/metrics-supported.md)

### <a name="accessing-metrics"></a>Metrikák elérése

> [!TIP]
> Az Azure CLI- vagy .NET-példák megtekintéséhez válassza ki az itt felsorolt megfelelő lapokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrikadefiníció felsorolása

Listába sorolhatja a tárfiók vagy a Queue Storage metrikadefinícióját. Használja a [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) parancsmagot.

Ebben a példában cserélje le a helyőrzőt a teljes tárfiók erőforrás-azonosítójára vagy az `<resource-ID>` üzenetsor erőforrás-azonosítójára. Ezeket az erőforrás-adattárat  a tárfiók Tulajdonságok oldalán találja a Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Metrikaértékek olvasása

A tárfiók vagy a felhőszolgáltatás fiókszintű metrikaértékét Queue Storage olvashatja. Használja a [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>A fiókszintű metrikadefiníció felsorolása

Listába sorolhatja a tárfiók vagy a Queue Storage metrikadefinícióját. Használja az [`az monitor metrics list-definitions`](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) parancsot.

Ebben a példában cserélje le a helyőrzőt a teljes tárfiók erőforrás-azonosítójára vagy az `<resource-ID>` üzenetsor erőforrás-azonosítójára. Ezeket az erőforrás-adattárat  a tárfiók Tulajdonságok oldalán találja a Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiókszintű metrikaértékek olvasása

Olvashatja a tárfiók vagy a Queue Storage metrikaértékét. Használja az [`az monitor metrics list`](/cli/azure/monitor/metrics#az_monitor_metrics_list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor [.NET SDK-t biztosít](https://www.nuget.org/packages/microsoft.azure.management.monitor/) a metrikadefiníciók és -értékek olvasásához. A [mintakód bemutatja,](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) hogyan használható az SDK különböző paraméterekkel. A tárolási metrikákhoz a vagy egy újabb verziót `0.18.0-preview` kell használnia.

Ezekben a példákban cserélje le a helyőrzőt a teljes tárfiók vagy az üzenetsor `<resource-ID>` erőforrás-azonosítójára. Ezeket az erőforrás-adattárat  a tárfiók Tulajdonságok oldalán találja a Azure Portal.

Cserélje le `<subscription-ID>` a változót az előfizetése azonosítójára. A , és értékeinek lekért információkért lásd: A portál használata erőforrásokhoz hozzáférő Azure AD-alkalmazás és `<tenant-ID>` `<application-ID>` `<AccessKey>` -szolgáltatásnév [létrehozásához.](../../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="list-the-account-level-metric-definition"></a>A fiókszintű metrikadefiníció felsorolása

Az alábbi példa bemutatja, hogyan listából választhatja ki a metrikadefiníciókat a fiók szintjén:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Fiókszintű metrikaértékek olvasása

Az alábbi példa bemutatja, hogyan olvashatja `UsedCapacity` be az adatokat a fiók szintjén:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Többdimenziós metrikaértékek olvasása

Többdimenziós metrikák esetén metaadatszűrőket kell meghatároznia, ha adott dimenzióértékek metrikaadatait szeretné olvasni.

Az alábbi példa bemutatja, hogyan olvashatja be a többdimenziós adatokat támogató metrikák metrikaadatokat:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

### <a name="template"></a>[Sablon](#tab/template)

N/A.

---

## <a name="analyzing-logs"></a>Naplók elemzése

Az erőforrásnaplókat elérheti üzenetsorként egy tárfiókban, eseményadatokként vagy Log Analytics-lekérdezésekkel.

A naplókban megjelenő mezők részletes leírását a monitorozási adatok Azure Queue Storage [tekintse meg.](monitor-queue-storage-reference.md)

> [!NOTE]
> Az Azure Storage Azure Monitor naplók nyilvános előzetes verzióban érhetők el, és előzetes verzióban tesztelhetőek az összes nyilvános felhőrégióban. Ez az előzetes verzió lehetővé teszi a blobok (beleértve a Azure Data Lake Storage Gen2), a fájlok, üzenetsorok, táblák, az általános célú v1 prémium szintű tárfiókok és az általános célú v2-tárfiókok naplóit. A klasszikus tárfiókok nem támogatottak.

Naplóbejegyzéseket csak akkor hoz létre a rendszer, ha kérések vannak a szolgáltatásvégponthoz. Ha például egy tárfiók tevékenységgel rendelkezik az üzenetsor végpontján, de a táblában vagy blobvégpontban nem, csak a Queue Storage naplók jön létre. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatáshoz való sikeres és sikertelen kérésekkel kapcsolatban. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kérelmek naplózása a lehető legjobb megoldás.

### <a name="log-authenticated-requests"></a>Hitelesített kérések naplózása

A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Közös hozzáférésű jogosultsági aláírást (SAS) vagy OAuth-hitelesítést használó kérések, beleértve a sikertelen és sikeres kéréseket
- Elemzési adatokra vonatkozó kérések (klasszikus naplóadatok a $logs **tárolóban** és osztálymetrikák adatai a $metric **táblákban)**

A szolgáltatás maga Queue Storage kérelmeket, például a naplók létrehozását vagy törlését, a rendszer nem naplózza. A naplózott adatok teljes listáját lásd: [Storage-naplózott](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) műveletek és állapotüzenetek és [Storage-naplóformátum.](monitor-queue-storage-reference.md)

### <a name="log-anonymous-requests"></a>Névtelen kérések naplózása

A rendszer a következő típusú névtelen kéréseket naplózza:

- Sikeres kérések
- Kiszolgálóhibák
- Időkorrel kapcsolatos hibák mind az ügyfél, mind a kiszolgáló esetén
- `GET`304-es hibakóddal meghiúsult kérések ( `Not Modified` )

A rendszer nem naplózza az összes többi sikertelen névtelen kérést. A naplózott adatok teljes listáját lásd: [Storage-naplózott](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) műveletek és állapotüzenetek és [Storage-naplóformátum.](monitor-queue-storage-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Naplók elérése tárfiókban

A naplók a cél tárfiókban található tárolóban tárolt blobokként jelennek meg. A rendszer egyetlen blobban gyűjti és tárolja az adatokat sorokkal tagolt JSON-adatként. A blob neve az alábbi elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Bemutatunk egy példát:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Naplók elérése egy eseményközpontban

Az eseményközpontba küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az eseményközpont megkapta-e a naplóadatokat. A Azure Portal az eseményközpontba, és ellenőrizze, hogy a szám `incoming requests` nagyobb-e nullánál.

![Naplók](media/monitor-queue-storage/event-hub-log.png)

Az eseményközpontba küldött naplóadatokhoz biztonsági információk, eseménykezelési és figyelési eszközök használatával férhet hozzá és olvashat. További információkért lásd: Mire használhatja az eseményközpontba küldött monitorozási [adatokat?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)részt.

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Naplók elérése Log Analytics-munkaterületen

A Log Analytics-munkaterületre küldött naplókhoz a naplólekérdezésekkel Azure Monitor férhet hozzá.

További információ: [A Log Analytics első](../../azure-monitor/logs/log-analytics-tutorial.md)lépések a Azure Monitor.

Az adatok a táblában `StorageQueueLogs` tárolódnak.

#### <a name="sample-kusto-queries"></a>Minta Kusto-lekérdezések

Az üzenetsorok figyelése a  naplók keresősávjában beírhat néhány lekérdezést. Ezek a lekérdezések az új [nyelvvel működnek.](../../azure-monitor/logs/log-query-overview.md)

> [!IMPORTANT]
> Amikor kiválasztja a **Naplók** elemet a tárfiók erőforráscsoport menüjében, a Log Analytics megnyílik, és a lekérdezés hatóköre az aktuális erőforráscsoportra van beállítva. Ez azt jelenti, hogy a naplólekérdezések csak az adott erőforráscsoport adatait tartalmazzák. Ha olyan lekérdezést szeretne futtatni, amely más erőforrásokból vagy  más Azure-szolgáltatásokból származó adatokat tartalmaz, válassza a Naplók lehetőséget a Azure Monitor **menüben.** A részleteket a Log [Analytics Azure Monitor log query scope and time range (Naplólekérdezés hatóköre és időtartománya)](../../azure-monitor/logs/scope.md) oldalon talál.

Ezekkel a lekérdezésekkel figyelheti az Azure Storage-fiókokat:

- Az elmúlt három nap 10 leggyakoribb hibáinak felsorolása.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- Az elmúlt három nap során a legtöbb hibát okozó 10 művelet felsorolása.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- Az első 10 művelet felsorolása az elmúlt három nap leghosszabb végpontok közötti késéssel.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- A kiszolgálóoldali szabályozási hibákat okozó összes művelet felsorolása az elmúlt három napban.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- A névtelen hozzáféréssel az elmúlt három napban lekért kérelmek felsorolása.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- Tortadiagram létrehozása az elmúlt három napban használt műveletekről.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="faq"></a>GYIK

**Támogatja az Azure Storage a felügyelt vagy a nem felügyelt lemezek metrikákat?**

Nem. A számítási példányok támogatják a lemezeken lévő metrikákat. További információ: [Lemezenkénti metrikák felügyelt](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)és nem felügyelt lemezek esetén.

## <a name="next-steps"></a>Következő lépések

- A monitorozási adatokra vonatkozó referencia Azure Queue Storage a Azure Queue Storage [metrikákat.](monitor-queue-storage-reference.md)
- Az Azure-erőforrások monitorozásával kapcsolatos részletekért lásd: [Azure-erőforrások monitorozása a Azure Monitor.](../../azure-monitor/essentials/monitor-azure-resource.md)
- További információ a metrikák migrálásról: [Azure Storage-metrikák migrálása.](../common/storage-metrics-migration.md)
