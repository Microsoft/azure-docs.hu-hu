---
title: Monitorozási Azure Files | Microsoft Docs
description: Megtudhatja, hogyan figyelheti a szolgáltatások teljesítményét és Azure Files. Figyelheti Azure Files adatait, megismerheti a konfigurációt, valamint elemezheti a metrika- és naplóadatokat.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 620ee3bc5978da4b274aed9a412679ae0835f0b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759826"
---
# <a name="monitoring-azure-files"></a>Figyelési Azure Files

Ha kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek Azure-erőforrásokra támaszkodnak, ezeket az erőforrásokat figyelni szeretné a rendelkezésre állásuk, teljesítményük és működésük szempontjából. Ez a cikk a Azure Files által létrehozott monitorozási adatokat ismerteti, és hogy hogyan használhatók a Azure Monitor ezen adatokra vonatkozó riasztások elemzésére.

## <a name="monitor-overview"></a>A figyelés áttekintése

Az **egyes** erőforrások Azure Portal áttekintési Azure Files az erőforrás-használat rövid áttekintését, például a kérelmeket és az óránkénti számlázást. Ezek az információk hasznosak, de csak kis mennyiségű monitorozási adat áll rendelkezésre. Ezen adatok egy része automatikusan gyűjthető, és az erőforrás létrehozása után elérhetővé válik az elemzéshez. Bizonyos konfigurációval további adatgyűjtési típusokat is engedélyezhet.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Azure Files adatokat hoz létre a [Azure Monitor](../../azure-monitor/overview.md)használatával, amely egy teljes körű monitorozási szolgáltatás az Azure-ban. Azure Monitor funkciók teljes készletét biztosítja a más felhőkben és a helyszínen található Azure-erőforrások és -erőforrások figyelése érdekében. 

Kezdje az [Azure-erőforrások monitorozása a Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)használatával cikkel, amely a következőket ismerteti:

- Mi az Azure Monitor?
- A monitorozással kapcsolatos költségek
- Az Azure-ban gyűjtött monitorozási adatok
- Adatgyűjtés konfigurálása
- Standard eszközök az Azure-ban az adatok monitorozásának elemzéséhez és riasztáshoz

A következő szakaszok erre a cikkre épülnek, és ismertetik a Azure Files. A példák bemutatják, hogyan konfigurálhatja az adatgyűjtést, és hogyan elemezheti ezeket az adatokat Azure-eszközökkel.

## <a name="monitoring-data"></a>Adatok monitorozása

Azure Files más Azure-erőforrásokhoz hasonló monitorozási adatokat gyűjt, amelyeket az Adatok monitorozása [Azure-erőforrásokból leírásban ismertetünk.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

A [metrikák](storage-files-monitoring-reference.md) és naplók metrikákról és naplókról részletes információkat az Azure-fájlfigyelési adatok referenciájában Azure Files.

A tárfiókok metrika- Azure Monitor naplói csak a Azure Resource Manager tárfiókokat támogatják. Azure Monitor nem támogatja a klasszikus tárfiókokat. Ha klasszikus tárfiókban szeretne metrikákat vagy naplókat használni, mihelyt egy Azure Resource Manager tárfiókba. Lásd: [Áttelepítés Azure Resource Manager.](../../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="collection-and-routing"></a>Gyűjtés és útválasztás

A rendszer automatikusan gyűjti a platformmetrikákat és a tevékenységnaplót, de egy diagnosztikai beállítással más helyekre is irányítható. 

Az erőforrásnaplók gyűjtéséhez létre kell hoznia egy diagnosztikai beállítást. A beállítás létrehozásakor válassza a **fájl** lehetőséget az a tárolótípus, amely számára engedélyezni szeretné a naplókat. Ezután adja meg az alábbi műveletkategóriák egyikét, amelyekhez naplókat szeretne gyűjteni. 

| Kategória | Leírás |
|:---|:---|
| StorageRead | Olvasási műveletek objektumokon. |
| StorageWrite | Írási műveletek objektumokon. |
| StorageDelete | Objektumokon lévő műveletek törlése. |

A naplózott SMB- és REST-műveletek listáját [](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a Storage naplózott műveleteit és állapotüzeneteit, valamint a monitorozási adatokra vonatkozó Azure Files [le.](storage-files-monitoring-reference.md)

## <a name="creating-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

Diagnosztikai beállításokat az Azure Portal, a PowerShell, az Azure CLI vagy egy Azure Resource Manager használatával hozhat létre.

> [!NOTE]
> Az Azure Storage Azure Monitor naplók nyilvános előzetes verzióban érhetők el, és előzetes verzióban tesztelhetőek az összes nyilvános felhőrégióban. Ez az előzetes verzió lehetővé teszi a blobok (beleértve a Azure Data Lake Storage Gen2), a fájlok, az üzenetsorok és a táblák naplóit. Ez a szolgáltatás az üzembe helyezési modellel létrehozott összes tárfiókhoz Azure Resource Manager érhető el. Lásd: [Tárfiók áttekintése.](../common/storage-account-overview.md)

Általános útmutatásért lásd: Diagnosztikai beállítás [létrehozása platformnaplók és -metrikák gyűjtéséhez az Azure-ban.](../../azure-monitor/essentials/diagnostic-settings.md)

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure Portalra.

2. Nyissa meg a tárfiókot.

3. A **Figyelés szakaszban** kattintson a Diagnosztikai **beállítások (előzetes verzió) elemre.**

   > [!div class="mx-imgBorder"]
   > ![portál – Diagnosztikai naplók](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Válassza **ki a** fájl típusú tárolót, amely számára engedélyezni szeretné a naplókat.

5. Kattintson **a Diagnosztikai beállítás hozzáadása elemre.**

   > [!div class="mx-imgBorder"]
   > ![portál – Erőforrásnaplók – diagnosztikai beállítás hozzáadása](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Megjelenik a Diagnosztikai **beállítások** lap.

   > [!div class="mx-imgBorder"]
   > ![Erőforrásnaplók oldal](media/storage-files-monitoring/diagnostic-logs-page.png)

6. Az oldal **Név** mezőjében adja meg az Erőforrásnapló-beállítás nevét. Ezután válassza ki, hogy mely műveleteket naplózza (olvasási, írási és törlési műveletek), és hová szeretné küldeni a naplókat.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása tárfiókba

Ha a naplókat egy tárfiókba archiválja, fizetnie kell a tárfiókba küldött naplókért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Jelölje be **az Archiválás tárfiókba** jelölőnégyzetet, majd kattintson a **Konfigurálás gombra.**

   > [!div class="mx-imgBorder"]   
   > ![Diagnosztikai beállítások laparchiválási tárhelye](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. A **Tárfiók legördülő** listában válassza ki azt a tárfiókot, amelybe a naplókat archiválni szeretné, kattintson az **OK** gombra, majd a Mentés **gombra.**

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Mielőtt kiválaszt egy tárfiókot exportálási [](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) célként, tekintse meg az Azure-erőforrásnaplók archiválását a tárfiók előfeltételeinek megtekintéséhez.

#### <a name="stream-logs-to-azure-event-hubs"></a>Naplók streamelése Azure Event Hubs

Ha úgy dönt, hogy a naplókat egy eseményközpontba streameli, fizetnie kell az eseményközpontba küldött naplók mennyiségért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor a Platformnaplók [című szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Jelölje be **a Stream egy eseményközpontba** jelölőnégyzetet, majd kattintson a **Konfigurálás** gombra.

2. Az **Eseményközpont** kiválasztása panelen válassza ki annak az eseményközpontnak a névterét, nevét és szabályzatnevét, amelybe a naplókat streamelni szeretné. 

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai beállítások lap eseményközpontja](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Kattintson az **OK** gombra, majd a **Mentés gombra.**

#### <a name="send-logs-to-azure-log-analytics"></a>Naplók küldése az Azure Log Analyticsbe

1. Jelölje be **a Küldés a Log Analyticsbe** jelölőnégyzetet, válasszon ki egy Log Analytics-munkaterületet, majd kattintson a elemre, majd kattintson a **Mentés gombra.**

   > [!div class="mx-imgBorder"]   
   > ![Diagnosztikai beállítások lap naplóelemzése](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

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

Ha úgy dönt, hogy a naplókat egy tárfiókba archiválja, akkor a tárfiókba küldött naplók mennyisége után fizet. Az egyes díjszabásokért tekintse **meg** a Azure Monitor a Platformnaplók [című szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat a [Set-AzDiagnosticSetting PowerShell-parancsmag](/powershell/module/az.monitor/set-azdiagnosticsetting) és a paraméter `StorageAccountId` használatával.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Cserélje le az ebben a kódrészletben található `<storage-service-resource--id>` helyőrzőt az Azure File service erőforrás-azonosítójára. Az erőforrás-azonosítót a Azure Portal tárfiók **Tulajdonságok** lapján találja.

A Category paraméter `StorageRead` értéke `StorageWrite` , és `StorageDelete` lehet. 

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Az egyes paraméterek leírását az Azure-erőforrásnaplók archiválásával az [Azure PowerShell.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Naplók streamelése egy eseményközpontba

Ha úgy dönt, hogy a naplókat egy eseményközpontba streameli, fizetnie kell az eseményközpontba küldött naplók mennyiségért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat a [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-parancsmag és a paraméter `EventHubAuthorizationRuleId` használatával.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Az egyes paraméterek leírását a [Stream Data to Event Hubs via PowerShell-parancsmagok () használatával.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat a [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-parancsmag és a paraméter `WorkspaceId` használatával.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

További információ: [Azure-erőforrásnaplók streamelése a Log Analytics-munkaterületre a Azure Monitor.](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Először nyissa meg a [Azure Cloud Shell,](../../cloud-shell/overview.md)vagy ha [](/cli/azure/install-azure-cli) helyileg telepítette az Azure CLI-t, nyisson meg egy parancskonzol-alkalmazást, például a Windows PowerShell.

2. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést annak a tárfióknak az előfizetéséhez, amely számára engedélyezni szeretné a naplókat.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása tárfiókba

Ha a naplókat egy tárfiókba archiválja, fizetnie kell a tárfiókba küldött naplókért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor lap [Platformnaplók szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat [az az monitor diagnostic-settings create paranccsal.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Cserélje le `<storage-service-resource--id>` a kódrészletben lévő helyőrzőt az erőforrás-azonosító Blob Storage szolgáltatásra. Az erőforrás-azonosítót a Azure Portal tárfiók **Tulajdonságok** lapján találja.

A category paraméter értéke `StorageRead` `StorageWrite` , és `StorageDelete` **lehet.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Az egyes paraméterek leírását az Archive Resource logs via the Azure CLI (Erőforrásnaplók [archiválása az Azure CLI-n keresztül) leírásában talál.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Naplók streamelése egy eseményközpontba

Ha úgy dönt, hogy a naplókat egy eseményközpontba streameli, fizetnie kell az eseményközpontba küldött naplók mennyiségért. Az egyes díjszabásokért tekintse **meg** a Azure Monitor a Platformnaplók [című szakaszát.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Engedélyezze a naplókat [az az monitor diagnostic-settings create paranccsal.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Az egyes paraméterek leírását az Azure CLI-n keresztül Event Hubs [Stream-adatok között.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat [az az monitor diagnostic-settings create paranccsal.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 További információ: [Azure-erőforrásnaplók streamelése a Log Analytics-munkaterületre a Azure Monitor.](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="template"></a>[Sablon](#tab/template)

Diagnosztikai beállítást Azure Resource Manager sablon megtekintéséhez tekintse meg az Azure Storage diagnosztikai [beállítását.](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)

---

## <a name="analyzing-metrics"></a>Metrikák elemzése

Az Azure Storage metrikai más Azure-szolgáltatásokból származó metrikák elemzéséhez használhatja a Metrikaböngésző. Nyissa Metrikaböngésző a **Metrikák lehetőséget a** Azure Monitor **menüből.** Az eszköz használatával kapcsolatos részletekért lásd: Ismerkedés az [Azure Metrikaböngésző.](../../azure-monitor/essentials/metrics-getting-started.md) 

A dimenziókat támogató metrikák esetén a kívánt dimenzióértékkel szűrheti a metrikát.  Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [Metrikadimenziók.](storage-files-monitoring-reference.md#metrics-dimensions) A Azure Files a következő névterek metrikai: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

A támogatott metrikákat Azure Monitor összes Azure Files itt Azure Monitor [meg.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)

### <a name="accessing-metrics"></a>Metrikák elérése

> [!TIP]
> Az Azure CLI- vagy .NET-példák megtekintéséhez válassza ki az itt felsorolt megfelelő lapokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrika definíciójának felsorolása

Felsorolhatja a tárfiók vagy a Azure Files metrikadefinícióját. Használja a [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) parancsmagot.

Ebben a példában cserélje le a helyőrzőt a teljes tárfiók erőforrás-azonosítójára vagy a Azure Files `<resource-ID>` erőforrás-azonosítójára.  Ezek az erőforrás-adatok a tárfiók **Tulajdonságok** oldalán találják meg a Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Metrikaértékek olvasása

Olvashatja a tárfiók vagy a Azure Files metrikaértékét. Használja a [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>A fiókszintű metrikadefiníciók felsorolása

Felsorolhatja a tárfiók vagy a Azure Files metrikadefinícióját. Használja az [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) parancsot.
 
Ebben a példában cserélje le a helyőrzőt a teljes tárfiók erőforrás-azonosítójára vagy a Azure Files `<resource-ID>` erőforrás-azonosítójára. Ezek az erőforrás-adatok a tárfiók **Tulajdonságok** oldalán találják meg a Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiókszintű metrikaértékek olvasása

Olvashatja a tárfiók vagy a Azure Files metrikaértékét. Használja az [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor [.NET SDK-t biztosít](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) a metrikadefiníciók és -értékek olvasása érdekében. A [mintakód bemutatja,](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) hogyan használható az SDK különböző paraméterekkel. A tárolási metrikákhoz a vagy egy újabb verziót `0.18.0-preview` kell használnia.
 
Ezekben a példákban cserélje le a helyőrzőt a teljes tárfiók vagy a Azure Files `<resource-ID>` erőforrás-azonosítójára. Ezeket az erőforrás-adattárat  a tárfiók Tulajdonságok oldalán találja a Azure Portal.

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
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

# <a name="template"></a>[Sablon](#tab/template)

N/A.

---

## <a name="analyzing-logs"></a>Naplók elemzése

Az erőforrásnaplókat blobként, egy tárfiókban, eseményadatként vagy Log Analytic-lekérdezésekkel is elérheti.

A naplózott SMB- és REST-műveletek listáját [](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a Storage naplózott műveleteit és állapotüzeneteit, valamint a monitorozási adatokra vonatkozó Azure Files [le.](storage-files-monitoring-reference.md)

> [!NOTE]
> Az Azure Storage-Azure Monitor nyilvános előzetes verzióban érhető el, és előzetes verzióban tesztelhető minden nyilvános felhőrégióban. Ez az előzetes verzió lehetővé teszi a blobok (például Azure Data Lake Storage Gen2), fájlok, üzenetsorok, táblák, prémium szintű tárfiókok naplóit az általános célú v1-ben és az általános célú v2-tárfiókokat. A klasszikus tárfiókok nem támogatottak.

A naplóbejegyzések csak akkor vannak létrehozva, ha kérések vannak a szolgáltatásvégpontra. Ha például egy tárfiók tevékenységgel rendelkezik a fájlvégpontjában, de a táblázat- vagy üzenetsorvégpontjaiban nem, csak az Azure File szolgáltatáshoz kapcsolódó naplók jön létre. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatáshoz való sikeres és sikertelen kérelmekről. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kérelmek naplózása a lehető legjobb megoldás alapján történik.

### <a name="log-authenticated-requests"></a>Hitelesített kérések naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Kerberos, NTLM vagy közös hozzáférésű jogosultságpélda (SAS) használatára vonatkozó kérések, beleértve a sikertelen és sikeres kéréseket
- Elemzési adatokra vonatkozó kérések (klasszikus naplóadatok a $logs **tárolóban** és klasszikus metrikaadatok a $metric **táblákban)**

A szolgáltatás maga Azure Files kérelmeket, például a naplók létrehozását vagy törlését, a rendszer nem naplózza. A naplózott SMB- és REST-kérelmek teljes listájáért lásd: [Storage-naplózott](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) műveletek és állapotüzenetek, valamint Azure Files [adatokra vonatkozó referencia.](storage-files-monitoring-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Hozzáférés a tárfiók naplóihoz

A naplók a cél tárfiókban lévő tárolóban tárolt blobokként jelennek meg. A rendszer egyetlen blobban gyűjti és tárolja az adatokat sorokkal tagolt JSON-adatként. A blob neve az alábbi elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Bemutatunk egy példát:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Naplók elérése egy eseményközpontban

Az eseményközpontba küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az eseményközpont megkapta-e a naplóadatokat. A Azure Portal az eseményközpontba, és ellenőrizze, hogy a bejövő **üzenetek** száma nagyobb-e nullánál. 

![Naplók](media/storage-files-monitoring/event-hub-log.png)

Az eseményközpontba küldött naplóadatokhoz biztonsági információk, eseménykezelési és figyelési eszközök használatával férhet hozzá és olvashat. További információ: Mire használhatja az eseményközpontba küldött monitorozási [adatokat?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Naplók elérése Log Analytics-munkaterületen

A Log Analytics-munkaterületre küldött naplókhoz a naplólekérdezésekkel Azure Monitor férhet hozzá. Az adatok a **StorageFileLogs táblában vannak tárolva.** 

További információ: [Log Analytics-oktatóanyag.](../../azure-monitor/logs/log-analytics-tutorial.md)

#### <a name="sample-kusto-queries"></a>Mintákat tartalmazó Kusto-lekérdezések

Íme néhány lekérdezés, amely  a Naplók keresősávba beírva segít megfigyelni a Azure Files. Ezek a lekérdezések az új [nyelvvel működnek.](../../azure-monitor/logs/log-query-overview.md)

> [!IMPORTANT]
> Amikor kiválasztja a **Naplók** elemet a tárfiók erőforráscsoport menüjében, a Log Analytics megnyílik, és a lekérdezés hatóköre az aktuális erőforráscsoportra van beállítva. Ez azt jelenti, hogy a naplólekérdezések csak az adott erőforráscsoport adatait tartalmazzák. Ha olyan lekérdezést szeretne futtatni, amely más erőforrásokból vagy  más Azure-szolgáltatásokból származó adatokat tartalmaz, válassza a Naplók lehetőséget a Azure Monitor **menüben.** A részleteket a Log Analytics Azure Monitor [log query scope and time range (Naplólekérdezés](../../azure-monitor/logs/scope.md) hatóköre és időtartománya) oldalon talál.

Ezekkel a lekérdezésekkel monitorhatja az Azure-fájlmegosztásokat:

- SMB-hibák megtekintése az elmúlt héten

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Tortadiagram létrehozása az SMB-műveletekről az elmúlt héten

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- REST-hibák megtekintése az elmúlt héten

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Tortadiagram létrehozása REST-műveletekről az elmúlt héten

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Az oszlopnevek és -leírások listájának megtekintéséhez lásd: [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs)Azure Files.

A lekérdezések írásával kapcsolatos további információkért lásd: [Log Analytics-oktatóanyag.](../../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="alerts"></a>Riasztások

Azure Monitor riasztások proaktívan értesítik, ha fontos feltételek találhatók a monitorozási adatokban. Lehetővé teszik a rendszer problémáinak azonosítását és megoldását, mielőtt az ügyfelek észrevené azokat. Riasztásokat állíthat be [](../../azure-monitor/alerts/alerts-metric-overview.md)a metrikákhoz, a [naplókhoz](../../azure-monitor/alerts/alerts-unified-log.md)és a [tevékenységnaplóhoz.](../../azure-monitor/alerts/activity-log-alerts.md) 

Az alábbi táblázat néhány példaforgatókönyvet mutat be, amelyek figyelése és a riasztáshoz megfelelő metrika használható:

| Eset | Riasztáshoz használt metrika |
|-|-|
| A fájlmegosztás le van szabályozás alatt. | Metrika: Tranzakciók<br>Dimenzió neve: Választípus <br>Dimenzió neve: Fájlmegosztás (csak prémium fájlmegosztás esetén) |
| A fájlmegosztás mérete a kapacitás 80%-a. | Metrika: Fájlkapacitás<br>Dimenzió neve: Fájlmegosztás (csak prémium fájlmegosztás esetén) |
| Egy nap alatt a fájlmegosztások ki- és behozott száma meghaladta az 500 GiB-t. | Metrika: Bejövő forgalom<br>Dimenzió neve: Fájlmegosztás (csak prémium fájlmegosztás esetén) |

### <a name="how-to-create-alerts-for-azure-files"></a>Riasztások létrehozása a Azure Files

1. A **tárfiókhoz a következő** **Azure Portal.** 

2. Kattintson **a Riasztások,** majd az **+ Új riasztási szabály elemre.**

3. Kattintson **az Erőforrás szerkesztése elemre,** válassza ki a Fájl **erőforrástípust,** majd kattintson a **Kész gombra.** 

4. Kattintson **a Feltétel hozzáadása** elemre, és adja meg a következő információkat a riasztáshoz: 

    - **Metrika**
    - **Dimenzió neve**
    - **Riasztási logika**

5. Kattintson **a Műveletcsoportok hozzáadása elemre,** és adjon hozzá egy műveletcsoportot (e-mail, SMS stb.) a riasztáshoz egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával.

6. Adja meg a Riasztás **részleteit,** például **a Riasztási szabály neve,** **Leírás** és **Súlyosság adatokat.**

7. Kattintson **a Riasztási szabály létrehozása elemre** a riasztás létrehozásához.

> [!NOTE]  
> Ha létrehoz egy riasztást, és az túl zajos, módosítsa a küszöbértéket és a riasztási logikát.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Riasztás létrehozása egy fájlmegosztás szabályozása esetén

1. A **tárfiókhoz a következő** **Azure Portal.**
2. A **Figyelés szakaszban** kattintson a **Riasztások** elemre, majd kattintson **az + Új riasztási szabály elemre.**
3. Kattintson **az Erőforrás szerkesztése elemre,** válassza ki a tárfiók fájlerőforrás-típusát, majd kattintson a Kész **gombra.**  Ha például a tárfiók neve `contoso` , válassza ki az `contoso/file` erőforrást.
4. Feltétel **hozzáadásához** kattintson a Feltétel hozzáadása lehetőségre.
5. Megjelenik a tárfiók által támogatott jelek listája. Válassza a **Tranzakciók metrikát.**
6. A **Jellogika konfigurálása panelen** kattintson a **Dimenzió neve** legördülő gombra, és válassza a **Választípus lehetőséget.**
7. Kattintson a **Dimenzióértékek** legördülő gombra, és válassza ki a fájlmegosztáshoz megfelelő választípusokat.

    Standard fájlmegosztások esetén válassza ki a következő választípusokat:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    Prémium fájlmegosztások esetén válassza ki a következő választípusokat:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Ha a választípusok nem  szerepelnek a Dimenzióértékek legördülő listában, az azt jelenti, hogy az erőforrás nem lett le szabályozás alatt. A dimenzióértékek hozzáadásához  a Dimenzióértékek legördülő lista mellett válassza az Egyéni érték hozzáadása **lehetőséget,** adja meg a respone típust (például **SuccessWithThrottling),** válassza az **OK** lehetőséget, majd ismételje meg ezeket a lépéseket a fájlmegosztás összes alkalmazható választípusának hozzáadásához.

8. Prémium **fájlmegosztások esetében kattintson** a Dimenzió **neve** legördülő gombra, és válassza a **Fájlmegosztás lehetőséget.** Standard **fájlmegosztások esetében** ugorjon a **következő lépésre: #10.**

   > [!NOTE]
   > Ha a fájlmegosztás egy szabványos  fájlmegosztás, a Fájlmegosztás dimenzió nem sorolja fel a fájlmegosztás(oka)t, mert a megosztásonkénti metrikák nem érhetők el a standard fájlmegosztások esetében. A standard fájlmegosztások szabályozási riasztásai akkor aktiválódnak, ha a tárfiók bármely fájlmegosztása le van szabályozás alatt, és a riasztás nem fogja azonosítani, hogy melyik fájlmegosztás lett lekorrkulva. Mivel a megosztásonkénti metrikák nem érhetők el a standard fájlmegosztások esetében, azt javasoljuk, hogy tárfiókonként egy fájlmegosztással rendelkezik.

9. Kattintson a **Dimenzióértékek** legördülő gombra, és válassza ki azokat a fájlmegosztásokat, amelyekről riasztást szeretne kapni.
10. Adja meg **a riasztás paramétereit** (küszöbérték, operátor, összesítés részletessége és kiértékelési gyakoriság), majd kattintson a **Kész gombra.**

    > [!TIP]
    > Ha statikus küszöbértéket használ, a metrikadiagram segíthet meghatározni egy ésszerű küszöbértéket, ha a fájlmegosztás jelenleg szabályozás alatt áll. Dinamikus küszöbérték használata esetén a metrikadiagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket.

11. Kattintson **a Műveletcsoportok hozzáadása** lehetőségre, ha egy műveletcsoportot (e-mailt, SMS-t stb.) szeretne hozzáadni a riasztáshoz egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával. 
12. Adja meg a Riasztás **részleteit,** például **a Riasztási szabály neve,** **Leírás** és **Súlyosság adatokat.**
13. Kattintson **a Riasztási szabály létrehozása elemre** a riasztás létrehozásához.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Riasztás létrehozása, ha az Azure-fájlmegosztás mérete a kapacitás 80%-a

1. A **tárfiókhoz a következő** **Azure Portal.**
2. A **Figyelés szakaszban** kattintson a **Riasztások,** majd az **+ Új riasztási szabály elemre.**
3. Kattintson **az Erőforrás szerkesztése elemre,** válassza ki a tárfiók fájlerőforrás-típusát, majd kattintson a Kész **gombra.**  Ha például a tárfiók neve `contoso` , válassza ki az `contoso/file` erőforrást.
4. Feltétel **hozzáadásához** kattintson a Feltétel hozzáadása lehetőségre.
5. Megjelenik a tárfiók által támogatott jelek listája. Válassza a Fájlkapacitás **metrikát.**
6. Prémium **fájlmegosztások esetében kattintson** a Dimenzió **neve** legördülő gombra, és válassza a **Fájlmegosztás lehetőséget.** Standard **fájlmegosztások esetében** ugorjon a következő **lépésre: #8.**

   > [!NOTE]
   > Ha a fájlmegosztás egy szabványos  fájlmegosztás, a Fájlmegosztás dimenzió nem sorolja fel a fájlmegosztás(oka)t, mert a megosztásonkénti metrikák nem érhetők el a standard fájlmegosztások esetében. A standard fájlmegosztások riasztásai a tárfiókban található összes fájlmegosztáson alapulnak. Mivel a megosztásonkénti metrikák nem érhetők el a standard fájlmegosztások esetében, azt javasoljuk, hogy tárfiókonként egy fájlmegosztással rendelkezik.

7. Kattintson **a Dimenzióértékek** legördülő gombra, és válassza ki azokat a fájlmegosztásokat, amelyekről riasztást szeretne kapni.
8. Adja meg **a Küszöbértéket** bájtban. Ha például a fájlmegosztás mérete 100 TiB, és riasztást szeretne kapni, ha a fájlmegosztás mérete a kapacitás 80%-a, akkor a bájtban megadott küszöbérték 8796093022080.
9. Határozza meg a többi **riasztási paramétert** (összesítés részletessége és a kiértékelés gyakorisága), majd kattintson a **Kész gombra.**
10. Kattintson **a Műveletcsoportok hozzáadása lehetőségre** egy műveletcsoport (e-mail, SMS stb.) a riasztáshoz való hozzáadásához egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával. 
11. Adja meg a **Riasztás részleteit,** például **a riasztási szabály nevét,** **leírását** és **súlyosságát.**
12. Kattintson **a Riasztási szabály létrehozása elemre** a riasztás létrehozásához.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Riasztás létrehozása, ha az Azure-fájlmegosztás napi 500 GiB-os korlátja túllépte az 500 GiB-t

1. A **tárfiókhoz a következő** **Azure Portal.**
2. A Figyelés szakaszban kattintson a **Riasztások,** majd az **+ Új riasztási szabály elemre.**
3. Kattintson **az Erőforrás szerkesztése elemre,** válassza ki a tárfiók fájlerőforrás-típusát, majd kattintson a Kész **gombra.**  Ha például a tárfiók neve contoso, válassza ki a contoso/file erőforrást.
4. Feltétel **hozzáadásához** kattintson a Feltétel hozzáadása lehetőségre.
5. Megjelenik a tárfiók által támogatott jelek listája, majd válassza a **Kigresszió** metrikát.
6. Prémium **fájlmegosztások esetében kattintson** a Dimenzió **neve** legördülő gombra, és válassza a **Fájlmegosztás lehetőséget.** Standard **fájlmegosztások esetében** ugorjon a **következő lépésre: #8.**

   > [!NOTE]
   > Ha a fájlmegosztás egy szabványos  fájlmegosztás, a Fájlmegosztás dimenzió nem sorolja fel a fájlmegosztás(oka)t, mert a megosztásonkénti metrikák nem érhetők el a standard fájlmegosztások esetében. A standard fájlmegosztások riasztásai a tárfiókban található összes fájlmegosztáson alapulnak. Mivel a megosztásonkénti metrikák nem érhetők el a standard fájlmegosztások esetében, azt javasoljuk, hogy tárfiókonként egy fájlmegosztással rendelkezik.

7. Kattintson a **Dimenzióértékek** legördülő gombra, és válassza ki azokat a fájlmegosztásokat, amelyekről riasztást szeretne kapni.
8. A Küszöbérték mezőben adja meg az **536870912000** bájt értéket. 
9. Kattintson az Összesítés részletessége legördülő **gombra,** és válassza a **24 óra lehetőséget.**
10. Válassza ki **a Kiértékelés gyakoriságát, majd** kattintson **a Kész gombra.**
11. Kattintson **a Műveletcsoportok hozzáadása** lehetőségre, ha egy műveletcsoportot (e-mailt, SMS-t stb.) szeretne hozzáadni a riasztáshoz egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával. 
12. Adja meg a Riasztás **részleteit,** például **a Riasztási szabály neve,** **Leírás** és **Súlyosság adatokat.**
13. Kattintson **a Riasztási szabály létrehozása elemre** a riasztás létrehozásához.

## <a name="next-steps"></a>Következő lépések

- [Azure Files adatok figyelése – referencia](storage-files-monitoring-reference.md)
- [Azure-erőforrások figyelése Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure Storage-metrikák migrálása](../common/storage-metrics-migration.md)
- [Azure Files üzembe helyezésének tervezése](./storage-files-planning.md)
- [Az Azure Files üzembe helyezése](./storage-how-to-create-file-share.md)
- [Azure-fájlok hibaelhárítása Windows rendszeren](./storage-troubleshoot-windows-file-connection-problems.md)
- [Azure-fájlok hibaelhárítása Linux rendszeren](./storage-troubleshoot-linux-file-connection-problems.md)
