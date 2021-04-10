---
title: Figyelési Azure Files | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Files teljesítményét és rendelkezésre állását. Azure Files adatok figyelése, a konfiguráció megismerése és a metrika és a naplók adatainak elemzése.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 16a899b7f0e40c2eee91d1dd445f0992572a9dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103418164"
---
# <a name="monitoring-azure-files"></a>Figyelés Azure Files

Ha olyan kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek az Azure-erőforrásokra támaszkodnak, ezeket az erőforrásokat szeretné figyelni a rendelkezésre állás, a teljesítmény és a művelet szempontjából. Ez a cikk ismerteti a Azure Files által generált figyelési információkat, valamint azt, hogy miként használhatók a Azure Monitor funkciói a riasztások elemzésére ezen az adattárban.

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes Azure Files erőforrásokhoz tartozó Azure Portal **áttekintő** lapja az erőforrás-használat, például a kérelmek és az óránkénti számlázás rövid áttekintését tartalmazza. Ezek az információk hasznosak, de csak kis mennyiségű figyelési adat érhető el. Az adatok némelyikét automatikusan gyűjti a rendszer, és az erőforrás létrehozásakor az elemzéshez azonnal elérhetővé válik. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
A Azure Files a [Azure monitor](../../azure-monitor/overview.md)használatával hoz létre megfigyelési adatgyűjtést, amely az Azure-ban teljes verem-figyelési szolgáltatás. A Azure Monitor a funkciók teljes készletét biztosítja az Azure-erőforrások és-erőforrások figyelésére más felhőkben és a helyszínen. 

A cikkből megtudhatja, hogyan [figyelheti az Azure-erőforrásokat a Azure monitorokkal](../../azure-monitor/essentials/monitor-azure-resource.md), amelyek a következőket ismertetik:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Filesból összegyűjtött adatok leírásával szerepel. A példák bemutatják, hogyan konfigurálhatja az adatgyűjtést, és hogyan elemezheti ezeket az Azure-eszközökkel.

## <a name="monitoring-data"></a>Adatok monitorozása

A Azure Files ugyanolyan típusú figyelési adatokat gyűjt, mint a többi Azure-erőforrást, amelyeket az [Azure-erőforrások adatainak monitorozása](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)című témakörben talál. 

A Azure Files által létrehozott metrikákkal és naplók metrikákkal kapcsolatos részletes információkat az [Azure file monitoring-adatokról](storage-files-monitoring-reference.md) szóló dokumentációban talál.

A Azure Monitor metrikái és naplói csak Azure Resource Manager Storage-fiókokat támogatják. A Azure Monitor nem támogatja a klasszikus Storage-fiókokat. Ha metrikákat vagy naplókat szeretne használni egy klasszikus Storage-fiókon, át kell telepítenie egy Azure Resource Manager Storage-fiókba. Lásd: [áttelepítés Azure Resource Managerra](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítás használatával más helyszínekre is átirányíthatja őket. 

Az erőforrás-naplók összegyűjtéséhez létre kell hoznia egy diagnosztikai beállítást. A beállítás létrehozásakor a **fájl** típusaként válassza ki a tároló típusát, amely számára engedélyezni szeretné a naplókat. Ezután adja meg az alábbi kategóriájú műveletek egyikét, amelyekhez naplókat szeretne gyűjteni. 

| Kategória | Leírás |
|:---|:---|
| StorageRead | Objektumok olvasási műveletei. |
| StorageWrite | Írási műveletek az objektumokon. |
| StorageDelete | Objektumok törlési műveletei. |

A naplózott SMB-és REST-műveletek listájának lekéréséhez tekintse meg a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [Azure Files figyelési adatok referenciáját](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

Diagnosztikai beállításokat a Azure Portal, a PowerShell, az Azure CLI vagy egy Azure Resource Manager sablon használatával hozhat létre.

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. Ez a funkció a Azure Resource Manager üzemi modellel létrehozott összes Storage-fiókhoz elérhető. Lásd: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

Általános útmutatásért lásd: [diagnosztikai beállítás létrehozása platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../../azure-monitor/essentials/diagnostic-settings.md).

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure Portalra.

2. Nyissa meg a tárfiókot.

3. A **figyelés** szakaszban kattintson a **diagnosztikai beállítások (előzetes verzió)** elemre.

   > [!div class="mx-imgBorder"]
   > ![portál – diagnosztikai naplók](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Válassza ki a **fájl** típusát a tárolóhoz, amely számára engedélyezni kívánja a naplókat.

5. Kattintson a **diagnosztikai beállítás hozzáadása** elemre.

   > [!div class="mx-imgBorder"]
   > ![portál – erőforrás-naplók – diagnosztikai beállítások hozzáadása](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Megjelenik a **diagnosztikai beállítások** lap.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás-naplók lap](media/storage-files-monitoring/diagnostic-logs-page.png)

6. A lap **név** mezőjébe írja be az erőforrás-napló beállításának nevét. Ezután adja meg, hogy mely műveletek legyenek naplózva (olvasási, írási és törlési műveletek), és hová szeretné elküldeni a naplókat.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása egy Storage-fiókba

Ha úgy dönt, hogy archiválja a naplókat egy Storage-fiókba, akkor a Storage-fiókba küldendő naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

1. Jelölje ki az **archívumot egy Storage-fiók** jelölőnégyzetet, majd kattintson a **Konfigurálás** gombra.

   > [!div class="mx-imgBorder"]   
   > ![Diagnosztikai beállítások lap Archive Storage](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. A **Storage-fiók** legördülő listában válassza ki azt a Storage-fiókot, amelybe archiválni szeretné a naplókat, kattintson az **OK** gombra, majd kattintson a **Mentés** gombra.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Mielőtt az Exportálás célhelyként kiválasztja a Storage-fiókot, tekintse meg az [Azure-erőforrás-naplók archiválása](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) című témakört az előfeltételek a Storage-fiókban való megismeréséhez.

#### <a name="stream-logs-to-azure-event-hubs"></a>Stream-naplók az Azure Event Hubs

Ha úgy dönt, hogy a naplókat egy Event hub-ba továbbítja, akkor az Event hub számára küldött naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

1. Jelölje ki az **adatfolyamot az Event hub** jelölőnégyzetbe, majd kattintson a **Konfigurálás** gombra.

2. Az **Event hub kiválasztása** ablaktáblán válassza ki annak az esemény-hubhoz a névterét, nevét és házirend-nevét, amelyre a naplókat továbbítani kívánja. 

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai beállítások lap Event hub](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Kattintson az **OK** gombra, majd kattintson a **Save (Mentés** ) gombra.

#### <a name="send-logs-to-azure-log-analytics"></a>Naplók elküldése az Azure Log Analyticsba

1. Jelölje be a **küldés log Analytics** jelölőnégyzetet, válassza ki a log Analytics-munkaterületet, majd kattintson a, majd a **Mentés** gombra.

   > [!div class="mx-imgBorder"]   
   > ![Diagnosztikai beállítások lap log Analytics](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nyisson meg egy Windows PowerShell-parancssorablakot, és jelentkezzen be az Azure-előfizetésbe az `Connect-AzAccount` parancs használatával. Ezután kövesse a képernyőn megjelenő utasításokat.

   ```powershell
   Connect-AzAccount
   ```

2. Állítsa be az aktív előfizetést azon Storage-fiók előfizetésére, amely számára engedélyezni szeretné a naplózást.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása egy Storage-fiókba

Ha úgy dönt, hogy archiválja a naplókat egy Storage-fiókba, akkor a Storage-fiókba küldendő naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

Engedélyezze a naplókat a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell-parancsmag használatával a `StorageAccountId` paraméterrel együtt.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Cserélje le az `<storage-service-resource--id>` ebben a kódrészletben található helyőrzőt az Azure file Service erőforrás-azonosítójával. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

`StorageRead` `StorageWrite` A (z), és a (z) `StorageDelete` paraméter  értékeként a (z), és értéket használhatja.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Az egyes paraméterek leírását az [Azure-erőforrás-naplók archiválása Azure PowerShell használatával](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)című részben tekintheti meg.

#### <a name="stream-logs-to-an-event-hub"></a>Adatfolyam-naplók az Event hub-ba

Ha úgy dönt, hogy a naplókat egy Event hub-ba továbbítja, akkor az Event hub számára küldött naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

Engedélyezze a naplókat a [set-AzDiagnosticSetting PowerShell-](/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmag használatával a `EventHubAuthorizationRuleId` paraméterrel.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Az egyes paraméterek leírását a következő témakörben találhatja [meg: stream-információk Event Hubs PowerShell-parancsmagok](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)használatával.

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat a [set-AzDiagnosticSetting PowerShell-](/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmag használatával a `WorkspaceId` paraméterrel.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

További információ: [stream Azure-erőforrás-naplók log Analytics munkaterületre Azure monitorban](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Először nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md), vagy ha helyileg [telepítette](/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. Ha az identitás egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését azon Storage-fiók előfizetésére, amelyhez engedélyezni szeretné a naplókat.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása egy Storage-fiókba

Ha úgy dönt, hogy archiválja a naplókat egy Storage-fiókba, akkor a Storage-fiókba küldendő naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

Engedélyezze a naplókat az az [monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Cserélje le az `<storage-service-resource--id>` ebben a kódrészletben található helyőrzőt az erőforrás-azonosító blob Storage szolgáltatással. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

`StorageRead` `StorageWrite` A (z), és a (z) `StorageDelete` paraméter  értékeként a (z), és értéket használhatja.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Az egyes paraméterek leírását az [erőforrás-naplók archiválása az Azure CLI használatával](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)című részben tekintheti meg.

#### <a name="stream-logs-to-an-event-hub"></a>Adatfolyam-naplók az Event hub-ba

Ha úgy dönt, hogy a naplókat egy Event hub-ba továbbítja, akkor az Event hub számára küldött naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

Engedélyezze a naplókat az az [monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Az egyes paraméterek leírását az [Azure CLI-n keresztül Event Hubs stream-információk](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)című részben tekintheti meg.

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat az az [monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 További információ: [stream Azure-erőforrás-naplók log Analytics munkaterületre Azure monitorban](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Sablon](#tab/template)

Diagnosztikai beállítást létrehozó Azure Resource Manager-sablon megtekintéséhez lásd: [diagnosztikai beállítások az Azure Storage szolgáltatáshoz](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

Az Azure Storage mérőszámait más Azure-szolgáltatásokból származó metrikákkal elemezheti Metrikaböngésző használatával. A **Azure monitor** menüből válassza a **metrikák** lehetőséget a Metrikaböngésző megnyitásához. Az eszköz használatáról további információt az [Azure Metrikaböngésző használatának első lépéseivel](../../azure-monitor/essentials/metrics-getting-started.md)foglalkozó témakörben talál. 

A dimenziókat támogató metrikák esetében szűrheti a mérőszámot a kívánt dimenzió értékkel.  Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [mérőszámok méretei](storage-files-monitoring-reference.md#metrics-dimensions). A Azure Files metrikái a következő névterekben találhatók: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/fileServices

A Azure Filest tartalmazó összes Azure Monitor támogatási mérőszámok listáját lásd: [Azure monitor támogatott metrikák](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Metrikák elérése

> [!TIP]
> Az Azure CLI-vagy .NET-példák megtekintéséhez válassza ki az itt felsorolt megfelelő lapokat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrika definíciójának listázása

A Storage-fiók vagy a Azure Files szolgáltatás metrikai definícióját listázhatja. Használja a [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) parancsmagot.

Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy a Azure Files szolgáltatás erőforrás-azonosítójával.  Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Metrika értékeinek olvasása

Elolvashatja a Storage-fiók vagy a Azure Files szolgáltatás fiók szintű metrikai értékeit. Használja a [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>A fiók szintű metrika definíciójának listázása

A Storage-fiók vagy a Azure Files szolgáltatás metrikai definícióját listázhatja. Használja az az [monitor Metrics List-fogalommeghatározások](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) parancsot.
 
Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy a Azure Files szolgáltatás erőforrás-azonosítójával. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Megtekintheti a Storage-fiók vagy a Azure Files szolgáltatás metrikai értékeit. Használja az az [monitor metrika List](/cli/azure/monitor/metrics#az-monitor-metrics-list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor biztosítja a [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) -t a metrika definíciójának és értékeinek olvasásához. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) azt mutatja be, hogyan használható az SDK különböző paraméterekkel. A `0.18.0-preview` tárolási metrikák esetében vagy újabb verziót kell használnia.
 
Ezekben a példákban cserélje le a `<resource-ID>` helyőrzőt a teljes Storage-fiók vagy a Azure Files szolgáltatás erőforrás-azonosítójával. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

Cserélje le a `<subscription-ID>` változót az előfizetés azonosítójával. A, a és a értékek értékének beszerzésével kapcsolatos útmutatásért `<tenant-ID>` `<application-ID>` `<AccessKey>` lásd: [a portál használata Azure ad-alkalmazás és-szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md). 

#### <a name="list-the-account-level-metric-definition"></a>A fiók szintű metrika definíciójának listázása

Az alábbi példa bemutatja, hogyan listázhat mérőszám-definíciót a fiók szintjén:

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

#### <a name="reading-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Az alábbi példa azt szemlélteti, hogyan olvashatók be `UsedCapacity` az információk a fiók szintjén:

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

#### <a name="reading-multidimensional-metric-values"></a>Többdimenziós metrika értékeinek olvasása

Többdimenziós metrikák esetében meg kell határoznia a metaadat-szűrőket, ha adott dimenzió értékeken szeretné beolvasni a metrikai adatokat.

Az alábbi példa bemutatja, hogyan olvashatja el a metrikus adatokat a többdimenziós támogatást támogató metrika esetében:

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

Az erőforrás-naplókat megadhatja blobként egy Storage-fiókban, az Event-adatként vagy a log analitikus lekérdezésekkel.

A naplózott SMB-és REST-műveletek listájának lekéréséhez tekintse meg a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [Azure Files figyelési adatok referenciáját](storage-files-monitoring-reference.md).

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák, a Premium Storage-fiókok általános célú v1-ben és az általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a fájl végpontján, de nem a tábla vagy a várólista végpontjában, akkor csak az Azure file Service-hez tartozó naplók jönnek létre. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatásokkal kapcsolatos sikeres és sikertelen kérelmekről. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

### <a name="log-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Kerberos, NTLM vagy közös hozzáférésű aláírást (SAS) használó kérelmek, beleértve a sikertelen és sikeres kérelmeket
- Elemzési adatokra irányuló kérelmek (a klasszikus naplózási adatok a **$logs** tárolóban és a klasszikus metrikai adatok a **$metric** táblákban)

A Azure Files szolgáltatás által kezdeményezett kérelmek (például a naplók létrehozása vagy törlése) nem kerülnek naplózásra. A naplózott SMB-és REST-kérelmek teljes listáját lásd: [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) , valamint [Azure Files monitorozási adatok referenciája](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Naplófájlok elérése a Storage-fiókban

A naplók úgy jelennek meg, mint a cél Storage-fiókban lévő tárolóban tárolt Blobok. Az adatok gyűjtése és tárolása egy blobon belül, egy sorba tagolt JSON-adattartalomként történik. A blob neve az alábbi elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Bemutatunk egy példát:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Eseménynaplók elérése az Event hub-ban

Az Event hub-nak küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az Event hub kapott-e naplózási adatokat. A Azure Portal nyissa meg az Event hub eszközt, és ellenőrizze, hogy a **Bejövő üzenetek** száma nagyobb-e nullánál. 

![Naplók](media/storage-files-monitoring/event-hub-log.png)

A biztonsági információkkal és az események kezelésével és figyelési eszközeivel elérheti és beolvashatja az Event hub-nak elküldett naplózási adatokat. További információ: Mit tehetek [az Event hub-ba küldött figyelési adatokkal?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Naplók elérése Log Analytics munkaterületen

A Log Analytics munkaterületre küldött naplók Azure Monitor naplózási lekérdezések használatával érhetők el. Az adattárolást a **StorageFileLogs** táblában tárolja a rendszer. 

További információ: [log Analytics oktatóanyag](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Íme néhány lekérdezés, amely megadható a **napló keresési** sávjában, hogy segítsen a Azure Files figyelésében. Ezek a lekérdezések az [új nyelvvel](../../azure-monitor/logs/log-query-overview.md)működnek.

> [!IMPORTANT]
> Amikor kiválasztja a **naplók** elemet a Storage-fiók erőforráscsoport menüjében, log Analytics megnyílik a lekérdezés hatóköre beállítással az aktuális erőforráscsoporthoz. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforráscsoport adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más erőforrásokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../../azure-monitor/logs/scope.md) .

Az alábbi lekérdezések segítségével figyelheti az Azure-fájlmegosztást:

- SMB-hibák megtekintése az elmúlt héten

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- SMB-műveletek kördiagramának létrehozása az elmúlt héten

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

- REST-műveletek kördiagramának létrehozása az elmúlt héten

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

A Azure Fileshoz tartozó oszlopnevek és leírások listájának megtekintéséhez lásd: [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs).

További információ a lekérdezések írásához: [log Analytics oktatóanyag](../../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="alerts"></a>Riasztások

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a rendszeren észlelt problémák azonosítását és megoldását, mielőtt az ügyfelek bejelentkeznek. Riasztásokat állíthat be a [metrikák](../../azure-monitor/alerts/alerts-metric-overview.md), [naplók](../../azure-monitor/alerts/alerts-unified-log.md)és a [tevékenység naplójában](../../azure-monitor/alerts/activity-log-alerts.md). 

A következő táblázat a riasztásra vonatkozó példákat és a riasztáshoz használandó megfelelő mérőszámot sorolja fel:

| Eset | A riasztáshoz használandó metrika |
|-|-|
| A fájlmegosztás szabályozása megtörténik. | Metrika: tranzakciók<br>Dimenzió neve: válasz típusa <br>Dimenzió neve: fájlmegosztás (csak prémium fájlmegosztás esetén) |
| A fájlmegosztás mérete a kapacitás 80%-a. | Metrika: fájl kapacitása<br>Dimenzió neve: fájlmegosztás (csak prémium fájlmegosztás esetén) |
| A fájlmegosztás egy nap alatt meghaladta a 500 GiB-t. | Metrika: kimenő forgalom<br>Dimenzió neve: fájlmegosztás (csak prémium fájlmegosztás esetén) |

### <a name="how-to-create-alerts-for-azure-files"></a>Riasztások létrehozása Azure Fileshoz

1. Nyissa meg a **Storage-fiókját** a **Azure Portal**. 

2. Kattintson a **riasztások** , majd az **+ új riasztási szabály** elemre.

3. Kattintson az **erőforrás szerkesztése** lehetőségre, válassza ki a **fájl erőforrás típusát** , majd kattintson a **kész** gombra. 

4. Kattintson a **feltétel hozzáadása** elemre, és adja meg a következő információkat a riasztáshoz: 

    - **Metrika**
    - **Dimenzió neve**
    - **Riasztási logika**

5. Kattintson a **műveleti csoportok hozzáadása** elemre, és adjon hozzá egy műveleti csoportot (E-mail, SMS stb.) a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.

6. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.

7. A riasztás létrehozásához kattintson a **riasztási szabály létrehozása** elemre.

> [!NOTE]  
> Ha riasztást hoz létre, és túl zajos, állítsa be a küszöbértéket és a riasztási logikát.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Riasztás létrehozása a fájlmegosztás szabályozása esetén

1. Nyissa meg a **Storage-fiókját** a **Azure Portal**.
2. A **figyelés** szakaszban kattintson a **riasztások** elemre, majd kattintson az **+ új riasztási szabály** elemre.
3. Kattintson az **erőforrás szerkesztése** lehetőségre, válassza ki a Storage-fiók **fájljának erőforrás-típusát** , majd kattintson a **kész** gombra. Ha például a Storage-fiók neve `contoso` , válassza ki az `contoso/file` erőforrást.
4. Feltétel hozzáadásához kattintson a **feltétel hozzáadása** elemre.
5. Ekkor megjelenik a Storage-fiók által támogatott jelek listája, és válassza ki a **tranzakciók** metrikáját.
6. A **jel logikai beállítása** panelen kattintson a **dimenzió neve** legördülő listára, és válassza a **Válasz típusa** lehetőséget.
7. Kattintson a **dimenzió értékek** legördülő listára, és válassza ki a megfelelő választ a fájlmegosztás számára.

    A standard fájlmegosztás esetében válassza ki a következő válaszokat:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    A prémium fájlmegosztás esetében válassza ki a következő válaszokat:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Ha a válaszok típusai nem szerepelnek a **dimenzió értékek** legördülő menüben, ez azt jelenti, hogy az erőforrás nincs szabályozva. A dimenzió értékeinek hozzáadásához a **dimenzióértékek** legördülő lista mellett válassza az **Egyéni érték hozzáadása** lehetőséget, adja meg a respone típusát (például **SuccessWithThrottling**), kattintson az **OK gombra**, majd ismételje meg ezeket a lépéseket a fájlmegosztás összes vonatkozó válaszának hozzáadásához.

8. **Prémium szintű fájlmegosztás** esetén kattintson a **dimenzió neve** legördülő menüre, majd válassza a **fájlmegosztás** lehetőséget. **Normál fájlmegosztás** esetén ugorjon a **#10 lépésre**.

   > [!NOTE]
   > Ha a fájlmegosztás szabványos fájlmegosztás, a **fájlmegosztás** dimenzió nem sorolja fel a fájlmegosztást (ka) t, mert a megosztási metrikák nem érhetők el a normál fájlmegosztás esetében. A normál fájlmegosztás esetén a rendszer elindítja a riasztásokat, ha a Storage-fiókon belül bármilyen fájlmegosztás szabályozva van, és a riasztás nem azonosítja, hogy mely fájlmegosztás lett szabályozva. Mivel a megosztási mérőszámok nem érhetők el a normál fájlmegosztás esetében, a javaslat egy fájlmegosztási fiókkal rendelkezik.

9. Kattintson a **dimenzió értékek** legördülő listára, és válassza ki azokat a fájlmegosztás (oka) t, amelyekről riasztást szeretne kapni.
10. Adja meg a **riasztási paramétereket** (a küszöbértéket, az operátort, az Összesítés részletességét és a kiértékelés gyakoriságát), majd kattintson a **kész** gombra.

    > [!TIP]
    > Ha statikus küszöbértéket használ, a metrikai diagram segíthet meghatározni egy ésszerű küszöbértéket, ha a fájlmegosztást jelenleg szabályozzák. Dinamikus küszöbérték használata esetén a metrika diagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket.

11. Kattintson a **műveleti csoportok hozzáadása** lehetőségre egy **műveleti csoport** (e-mail, SMS stb.) a riasztáshoz való hozzáadásához, vagy egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.
12. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.
13. A riasztás létrehozásához kattintson a **riasztási szabály létrehozása** elemre.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Riasztás létrehozása, ha az Azure-fájlmegosztás mérete a kapacitás 80%-a

1. Nyissa meg a **Storage-fiókját** a **Azure Portal**.
2. A **figyelés** szakaszban kattintson a **riasztások** , majd az **+ új riasztási szabály** elemre.
3. Kattintson az **erőforrás szerkesztése** lehetőségre, válassza ki a Storage-fiók **fájljának erőforrás-típusát** , majd kattintson a **kész** gombra. Ha például a Storage-fiók neve `contoso` , válassza ki az `contoso/file` erőforrást.
4. Feltétel hozzáadásához kattintson a **feltétel hozzáadása** elemre.
5. Ekkor megjelenik a Storage-fiók által támogatott jelek listája, és válassza ki a **fájl kapacitása** mérőszámot.
6. **Prémium szintű fájlmegosztás** esetén kattintson a **dimenzió neve** legördülő menüre, majd válassza a **fájlmegosztás** lehetőséget. **Normál fájlmegosztás** esetén ugorjon a **#8 lépésre**.

   > [!NOTE]
   > Ha a fájlmegosztás szabványos fájlmegosztás, a **fájlmegosztás** dimenzió nem sorolja fel a fájlmegosztást (ka) t, mert a megosztási metrikák nem érhetők el a normál fájlmegosztás esetében. A normál fájlmegosztás riasztásai a Storage-fiókban lévő összes fájlmegosztás alapján vannak kiosztva. Mivel a megosztási mérőszámok nem érhetők el a normál fájlmegosztás esetében, a javaslat egy fájlmegosztási fiókkal rendelkezik.

7. Kattintson a **dimenzió értékek** legördülő listára, és válassza ki azokat a fájlmegosztás (oka) t, amelyekről riasztást szeretne kapni.
8. Adja meg a **küszöbértéket** bájtban megadva. Ha például a fájlmegosztás mérete 100 TiB, és riasztást szeretne kapni, ha a fájlmegosztás mérete a kapacitás 80%-ában van megadva, akkor a küszöbérték értéke 87960930222080.
9. Adja meg a **riasztási paraméterek** hátralévő részét (az Összesítés részletességét és a kiértékelés gyakoriságát), majd kattintson a **kész** gombra.
10. Kattintson a **műveleti csoportok hozzáadása** lehetőségre egy **műveleti csoport** (e-mail, SMS stb.) a riasztáshoz való hozzáadásához, vagy egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.
11. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.
12. A riasztás létrehozásához kattintson a **riasztási szabály létrehozása** elemre.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Riasztás létrehozása, ha az Azure-fájlmegosztás kimenő adatforgalmának napja túllépte a 500 GiB-t

1. Nyissa meg a **Storage-fiókját** a **Azure Portal**.
2. A figyelés szakaszban kattintson a **riasztások** , majd az **+ új riasztási szabály** elemre.
3. Kattintson az **erőforrás szerkesztése** lehetőségre, válassza ki a Storage-fiók **fájljának erőforrás-típusát** , majd kattintson a **kész** gombra. Ha például a Storage-fiók neve contoso, válassza ki a contoso/fájl erőforrást.
4. Feltétel hozzáadásához kattintson a **feltétel hozzáadása** elemre.
5. Ekkor megjelenik a Storage-fiók által támogatott jelek listája, és válassza ki a **kimenő** forgalom metrikáját.
6. **Prémium szintű fájlmegosztás** esetén kattintson a **dimenzió neve** legördülő menüre, majd válassza a **fájlmegosztás** lehetőséget. **Normál fájlmegosztás** esetén ugorjon a **#8 lépésre**.

   > [!NOTE]
   > Ha a fájlmegosztás szabványos fájlmegosztás, a **fájlmegosztás** dimenzió nem sorolja fel a fájlmegosztást (ka) t, mert a megosztási metrikák nem érhetők el a normál fájlmegosztás esetében. A normál fájlmegosztás riasztásai a Storage-fiókban lévő összes fájlmegosztás alapján vannak kiosztva. Mivel a megosztási mérőszámok nem érhetők el a normál fájlmegosztás esetében, a javaslat egy fájlmegosztási fiókkal rendelkezik.

7. Kattintson a **dimenzió értékek** legördülő listára, és válassza ki azokat a fájlmegosztás (oka) t, amelyekről riasztást szeretne kapni.
8. Adja meg a **536870912000** bájtos értéket a küszöbértékhez. 
9. Kattintson az **összesítési részletesség** legördülő listára, és válassza a **24 óra** lehetőséget.
10. Válassza ki az **értékelés gyakoriságát** , és **kattintson a kész gombra**.
11. Kattintson a **műveleti csoportok hozzáadása** lehetőségre egy **műveleti csoport** (e-mail, SMS stb.) a riasztáshoz való hozzáadásához, vagy egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.
12. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.
13. A riasztás létrehozásához kattintson a **riasztási szabály létrehozása** elemre.

## <a name="next-steps"></a>Következő lépések

- [Azure Files monitorozási adathivatkozás](storage-files-monitoring-reference.md)
- [Azure-erőforrások figyelése Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure Storage-metrikák migrálása](../common/storage-metrics-migration.md)
- [Azure Files üzembe helyezésének tervezése](./storage-files-planning.md)
- [Az Azure Files üzembe helyezése](./storage-how-to-create-file-share.md)
- [Azure-fájlok hibaelhárítása Windows rendszeren](./storage-troubleshoot-windows-file-connection-problems.md)
- [Azure-fájlok hibaelhárítása Linux rendszeren](./storage-troubleshoot-linux-file-connection-problems.md)
