---
title: Az Azure Blob Storage monitorozása | Microsoft Docs
description: Ismerje meg, hogyan figyelheti az Azure Blob Storage teljesítményét és rendelkezésre állását. Az Azure Blob Storage adatainak figyelése, a konfiguráció megismerése és a metrika és a naplózási adatok elemzése.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 3b497a8507fb82bfb69fbe7396e5a0d34006a7f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502076"
---
# <a name="monitoring-azure-blob-storage"></a>Az Azure Blob Storage figyelése

Ha olyan kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek az Azure-erőforrásokra támaszkodnak, ezeket az erőforrásokat szeretné figyelni a rendelkezésre állás, a teljesítmény és a művelet szempontjából. Ez a cikk az Azure Blob Storage által generált figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói a riasztások elemzésére ezen az adattárban.

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. Ez a funkció a Azure Resource Manager üzemi modellel létrehozott összes Storage-fiókhoz elérhető. Lásd: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes blob Storage-erőforrások Azure Portal **áttekintő** lapja az erőforrások felhasználásának rövid nézetét tartalmazza, például a kérelmeket és az óránkénti számlázást. Ezek az információk hasznosak, de csak kis mennyiségű figyelési adat érhető el. Az adatok némelyikét automatikusan gyűjti a rendszer, és az erőforrás létrehozásakor az elemzéshez azonnal elérhetővé válik. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az Azure Blob Storage a [Azure monitor](../../azure-monitor/overview.md)használatával hozza létre a figyelési adataikat, amely az Azure-ban teljes verem-figyelési szolgáltatás. A Azure Monitor a funkciók teljes készletét biztosítja az Azure-erőforrások és-erőforrások figyelésére más felhőkben és a helyszínen. 

A cikkből megtudhatja [, hogyan figyelheti az Azure-erőforrásokat a Azure monitor,](../../azure-monitor/essentials/monitor-azure-resource.md) amely leírja a következőket:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi, az Azure Storage-ból gyűjtött adatok leírásával a cikk a következő részekre épül. A példák bemutatják, hogyan konfigurálhatja az adatgyűjtést, és hogyan elemezheti ezeket az Azure-eszközökkel.

## <a name="monitoring-data"></a>Adatok monitorozása

Az Azure Blob Storage ugyanolyan típusú figyelési adatokat gyűjt, mint az Azure- [erőforrások monitorozásával](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)kapcsolatos egyéb Azure-erőforrások. 

Az Azure Blob Storage által létrehozott mérőszámokra és naplókra vonatkozó részletes információkért tekintse meg az [azure blob Storage monitorozási adatok referenciáját](monitor-blob-storage-reference.md) .

A Azure Monitor metrikái és naplói csak Azure Resource Manager Storage-fiókokat támogatják. A Azure Monitor nem támogatja a klasszikus Storage-fiókokat. Ha metrikákat vagy naplókat szeretne használni egy klasszikus Storage-fiókon, át kell telepítenie egy Azure Resource Manager Storage-fiókba. Lásd: [áttelepítés Azure Resource Managerra](../../virtual-machines/migration-classic-resource-manager-overview.md).

Ha szeretné, továbbra is használhatja a klasszikus metrikákat és naplókat. Valójában a klasszikus metrikák és naplók párhuzamosan érhetők el Azure Monitor metrikákkal és naplókkal. A támogatás addig marad érvényben, amíg az Azure Storage befejezi a szolgáltatást az örökölt metrikák és naplók esetében.

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítás használatával más helyszínekre is átirányíthatja őket. 

Az erőforrás-naplók összegyűjtéséhez létre kell hoznia egy diagnosztikai beállítást. A beállítás létrehozásakor válassza a **blob** lehetőséget a tárolók számára, amelyeken engedélyezni szeretné a naplókat. Ezután adja meg az alábbi kategóriájú műveletek egyikét, amelyekhez naplókat szeretne gyűjteni. 

| Kategória | Leírás |
|:---|:---|
| StorageRead | Objektumok olvasási műveletei. |
| StorageWrite | Írási műveletek az objektumokon. |
| StorageDelete | Objektumok törlési műveletei. |

> [!NOTE]
> Data Lake Storage Gen2 nem jelenik meg tárolási típusként. Ennek az az oka, hogy Data Lake Storage Gen2 a blob Storage számára elérhető képességek halmaza. 

## <a name="creating-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

Diagnosztikai beállításokat a Azure Portal, a PowerShell, az Azure CLI vagy egy Azure Resource Manager sablon használatával hozhat létre. 

Általános útmutatásért lásd: [diagnosztikai beállítás létrehozása platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. Ez a funkció a Azure Resource Manager üzemi modellel létrehozott összes Storage-fiókhoz elérhető. Lásd: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure Portalra.

2. Nyissa meg a tárfiókot.

3. A **figyelés** szakaszban kattintson a **diagnosztikai beállítások (előzetes verzió)** elemre.

   > [!div class="mx-imgBorder"]
   > ![portál – diagnosztikai naplók](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Válassza ki a **blobot** olyan tárolási típusként, amely számára engedélyezni kívánja a naplókat.

5. Kattintson a **diagnosztikai beállítás hozzáadása** elemre.

   > [!div class="mx-imgBorder"]
   > ![portál – erőforrás-naplók – diagnosztikai beállítások hozzáadása](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   Megjelenik a **diagnosztikai beállítások** lap.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás-naplók lap](media/monitor-blob-storage/diagnostic-logs-page.png)

6. A lap **név** mezőjébe írja be az erőforrás-napló beállításának nevét. Ezután adja meg, hogy mely műveletek legyenek naplózva (olvasási, írási és törlési műveletek), és hová szeretné elküldeni a naplókat.

#### <a name="archive-logs-to-a-storage-account"></a>Naplók archiválása egy Storage-fiókba

Ha úgy dönt, hogy archiválja a naplókat egy Storage-fiókba, akkor a Storage-fiókba küldendő naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

1. Jelölje ki az **archívumot egy Storage-fiók** jelölőnégyzetet, majd kattintson a **Konfigurálás** gombra.

   > [!div class="mx-imgBorder"]   
   > ![Diagnosztikai beállítások lap Archive Storage](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. A **Storage-fiók** legördülő listában válassza ki azt a Storage-fiókot, amelybe archiválni szeretné a naplókat, kattintson az **OK** gombra, majd kattintson a **Mentés** gombra.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Mielőtt az Exportálás célhelyként kiválasztja a Storage-fiókot, tekintse meg az [Azure-erőforrás-naplók archiválása](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) című témakört az előfeltételek a Storage-fiókban való megismeréséhez.

#### <a name="stream-logs-to-azure-event-hubs"></a>Stream-naplók az Azure Event Hubs

Ha úgy dönt, hogy a naplókat egy Event hub-ba továbbítja, akkor az Event hub számára küldött naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

1. Jelölje ki az **adatfolyamot az Event hub** jelölőnégyzetbe, majd kattintson a **Konfigurálás** gombra.

2. Az **Event hub kiválasztása** ablaktáblán válassza ki annak az esemény-hubhoz a névterét, nevét és házirend-nevét, amelyre a naplókat továbbítani kívánja. 

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai beállítások lap Event hub](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Kattintson az **OK** gombra, majd kattintson a **Save (Mentés** ) gombra.

#### <a name="send-logs-to-azure-log-analytics"></a>Naplók elküldése az Azure Log Analyticsba

1. Jelölje be a **küldés log Analytics** jelölőnégyzetet, válasszon ki egy log Analytics-munkaterületet, majd kattintson a **Save (Mentés** ) gombra.

   > [!div class="mx-imgBorder"]   
   > ![Diagnosztikai beállítások lap log Analytics](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

Cserélje le az `<storage-service-resource--id>` ebben a kódrészletben található helyőrzőt a blob szolgáltatás erőforrás-azonosítójával. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

`StorageRead` `StorageWrite` A (z), és a (z) `StorageDelete` paraméter  értékeként a (z), és értéket használhatja.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Az egyes paraméterek leírását az [Azure-erőforrás-naplók archiválása Azure PowerShell használatával](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)című részben tekintheti meg.

#### <a name="stream-logs-to-an-event-hub"></a>Adatfolyam-naplók az Event hub-ba

Ha úgy dönt, hogy a naplókat egy Event hub-ba továbbítja, akkor az Event hub számára küldött naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

Engedélyezze a naplókat a [set-AzDiagnosticSetting PowerShell-](/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmag használatával a `EventHubAuthorizationRuleId` paraméterrel.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Az egyes paraméterek leírását a következő témakörben találhatja [meg: stream-információk Event Hubs PowerShell-parancsmagok](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)használatával.

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat a [set-AzDiagnosticSetting PowerShell-](/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmag használatával a `WorkspaceId` paraméterrel.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Bemutatunk egy példát:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true }]'
```

Cserélje le az `<storage-service-resource--id>` ebben a kódrészletben található helyőrzőt az erőforrás-azonosító blob Storage szolgáltatással. A Azure Portal erőforrás-AZONOSÍTÓját a Storage-fiók **tulajdonságlapjának** megnyitásával érheti el.

`StorageRead` `StorageWrite` A (z), és a (z) `StorageDelete` paraméter  értékeként a (z), és értéket használhatja.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite}]'`

Az egyes paraméterek leírását az [erőforrás-naplók archiválása az Azure CLI használatával](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)című részben tekintheti meg.

#### <a name="stream-logs-to-an-event-hub"></a>Adatfolyam-naplók az Event hub-ba

Ha úgy dönt, hogy a naplókat egy Event hub-ba továbbítja, akkor az Event hub számára küldött naplók mennyiségét kell megfizetnie. A díjszabással kapcsolatos további információkért tekintse meg az [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) oldal **platform naplók** szakaszát.

Engedélyezze a naplókat az az [monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Az egyes paraméterek leírását az [Azure CLI-n keresztül Event Hubs stream-információk](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)című részben tekintheti meg.

#### <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

Engedélyezze a naplókat az az [monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) paranccsal.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Bemutatunk egy példát:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 További információ: [stream Azure-erőforrás-naplók log Analytics munkaterületre Azure monitorban](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Sablon](#tab/template)

Diagnosztikai beállítást létrehozó Azure Resource Manager-sablon megtekintéséhez lásd: [diagnosztikai beállítások az Azure Storage szolgáltatáshoz](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

Az Azure Storage mérőszámait más Azure-szolgáltatásokból származó metrikákkal elemezheti Metrikaböngésző használatával. A **Azure monitor** menüből válassza a **metrikák** lehetőséget a Metrikaböngésző megnyitásához. Az eszköz használatáról további információt az [Azure Metrikaböngésző használatának első lépéseivel](../../azure-monitor/essentials/metrics-getting-started.md)foglalkozó témakörben talál. 

Ebből a példából megtudhatja, hogyan tekintheti meg a **tranzakciókat** a fiók szintjén.

![Képernyőfelvétel a metrikák eléréséről a Azure Portal](./media/monitor-blob-storage/access-metrics-portal.png)

A dimenziókat támogató metrikák esetében szűrheti a mérőszámot a kívánt dimenzió értékkel. Ebből a példából megtudhatja, hogyan tekintheti meg a **tranzakciókat** a fiók szintjén egy adott művelethez az **API-név** dimenzió értékeinek kiválasztásával.

![Képernyőfelvétel a Azure Portal dimenzióval rendelkező metrikák eléréséről](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Az Azure Storage által támogatott dimenziók teljes listájáért lásd: [mérőszámok méretei](monitor-blob-storage-reference.md#metrics-dimensions).

Az Azure Blob Storage mérőszámai ezekben a névterekben találhatók: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices

Az Azure Blob Storaget tartalmazó összes Azure Monitor támogatási mérőszámok listáját itt tekintheti meg: [Azure monitor támogatott metrikák](../../azure-monitor/essentials/metrics-supported.md).


### <a name="accessing-metrics"></a>Metrikák elérése

> [!TIP]
> Az Azure CLI-vagy .NET-példák megtekintéséhez válassza ki az itt felsorolt megfelelő lapokat.

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor biztosítja a [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) -t a metrika definíciójának és értékeinek olvasásához. A [mintakód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) azt mutatja be, hogyan használható az SDK különböző paraméterekkel. A `0.18.0-preview` tárolási metrikák esetében vagy újabb verziót kell használnia.
 
Ezekben a példákban cserélje le a `<resource-ID>` helyőrzőt a teljes Storage-fiók vagy a blob Storage szolgáltatás erőforrás-azonosítójával. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

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
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>A metrika definíciójának listázása

A Storage-fiók vagy a blob Storage szolgáltatás metrika-definícióját is listázhatja. Használja a [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) parancsmagot.

Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy a blob Storage szolgáltatás erőforrás-azonosítójával.  Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Metrika értékeinek olvasása

Elolvashatja a Storage-fiók vagy a blob Storage szolgáltatás fiók szintű metrikai értékeit is. Használja a [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) parancsmagot.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>A fiók szintű metrika definíciójának listázása

A Storage-fiók vagy a blob Storage szolgáltatás metrika-definícióját is listázhatja. Használja az az [monitor Metrics List-fogalommeghatározások](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) parancsot.
 
Ebben a példában a helyőrzőt cserélje le a `<resource-ID>` teljes Storage-fiók erőforrás-azonosítójával vagy a blob Storage szolgáltatás erőforrás-azonosítójával. Ezek az erőforrás-azonosítók a Storage-fiók **tulajdonságlapján** találhatók a Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Fiók szintű metrika értékeinek olvasása

Megtekintheti a Storage-fiók vagy a blob Storage szolgáltatás metrikai értékeit. Használja az az [monitor metrika List](/cli/azure/monitor/metrics#az-monitor-metrics-list) parancsot.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Sablon](#tab/template)

N/A.

---

## <a name="analyzing-logs"></a>Naplók elemzése

Az erőforrás-naplókat megadhatja blobként egy Storage-fiókban, az Event-adatként vagy a log analitikus lekérdezésekkel.

Az ezekben a naplókban megjelenő mezők részletes ismertetését lásd: [Azure Blob Storage monitoring-információk referenciája](monitor-blob-storage-reference.md).

> [!NOTE]
> Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák, a Premium Storage-fiókok általános célú v1-ben és az általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a tábla vagy a várólista végpontján, akkor csak a blob szolgáltatáshoz tartozó naplók jönnek létre. Az Azure Storage-naplók részletes információkat tartalmaznak a tárolási szolgáltatásokkal kapcsolatos sikeres és sikertelen kérelmekről. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

### <a name="log-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Megosztott elérési aláírást (SAS) vagy OAuth használó kérelmek, beleértve a sikertelen és sikeres kérelmeket
- Elemzési adatokra irányuló kérelmek (a klasszikus naplózási adatok a **$logs** tárolóban és az osztály metrikájának adatai a **$metric** táblákban)

A blob Storage szolgáltatás által kezdeményezett kérelmek (például a naplók létrehozása vagy törlése) nem kerülnek naplózásra. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási napló formátuma](monitor-blob-storage-reference.md)című témakörben tekintheti meg.

### <a name="log-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérések
- Kiszolgálóhibák
- Időtúllépési hibák az ügyfél és a kiszolgáló esetében
- Sikertelen GET kérelmek a 304 hibakódmal (nincs módosítva)

Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját a [Storage naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és a [tárolási napló formátuma](monitor-blob-storage-reference.md)című témakörben tekintheti meg.

### <a name="accessing-logs-in-a-storage-account"></a>Naplófájlok elérése a Storage-fiókban

A naplók úgy jelennek meg, mint a cél Storage-fiókban lévő tárolóban tárolt Blobok. Az adatok gyűjtése és tárolása egy blobon belül, egy sorba tagolt JSON-adattartalomként történik. A blob neve az alábbi elnevezési konvenciót követi:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Bemutatunk egy példát:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Eseménynaplók elérése az Event hub-ban

Az Event hub-nak küldött naplók nem fájlként vannak tárolva, de ellenőrizheti, hogy az Event hub kapott-e naplózási adatokat. A Azure Portal nyissa meg az Event hub eszközt, és ellenőrizze, hogy a **Bejövő üzenetek** száma nagyobb-e nullánál. 

![Naplók](media/monitor-blob-storage/event-hub-log.png)

A biztonsági információkkal és az események kezelésével és figyelési eszközeivel elérheti és beolvashatja az Event hub-nak elküldett naplózási adatokat. További információ: Mit tehetek [az Event hub-ba küldött figyelési adatokkal?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Naplók elérése Log Analytics munkaterületen

A Log Analytics munkaterületre küldött naplók Azure Monitor naplózási lekérdezések használatával érhetők el.

További információ: [Bevezetés a log Analytics használatába Azure monitor](../../azure-monitor/logs/log-analytics-tutorial.md).

Az adattárolást a **StorageBlobLog** táblában tárolja a rendszer. A Data Lake Storage Gen2 naplói nem jelennek meg egy dedikált táblában. Ennek oka, hogy Data Lake Storage Gen2 nem szolgáltatás. Ez egy olyan funkció, amelyet engedélyezheti a Storage-fiókban. Ha engedélyezte ezeket a képességeket, a naplók továbbra is megjelennek a StorageBlobLogs táblában. 

#### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Íme néhány lekérdezés, amely megadható a **log keresési** sávban a blob Storage figyeléséhez. Ezek a lekérdezések az [új nyelvvel](../../azure-monitor/logs/log-query-overview.md)működnek.

> [!IMPORTANT]
> Amikor kiválasztja a **naplók** elemet a Storage-fiók erőforráscsoport menüjében, log Analytics megnyílik a lekérdezés hatóköre beállítással az aktuális erőforráscsoporthoz. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforráscsoport adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más erőforrásokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../../azure-monitor/logs/scope.md) .

Az alábbi lekérdezések segítségével figyelheti az Azure Storage-fiókokat:

* A 10 leggyakoribb hiba listázása az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* A legtöbb hibát okozó tíz művelet listázása az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Az elmúlt három nap során a leghosszabb késéssel rendelkező 10 művelet listázása.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Az összes olyan művelet listázása, amely a kiszolgálóoldali sávszélesség-szabályozási hibákat okozta az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Az összes névtelen hozzáféréssel rendelkező kérelem listázása az elmúlt három napban.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Az elmúlt három napban használt műveletek kördiagramának létrehozása.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>GYIK

**Támogatja az Azure Storage a Managed Disks és a nem felügyelt lemezek metrikáit?**

Nem. Az Azure-beli számítási szolgáltatás támogatja a lemezek mérőszámait. További információ: [a felügyelt és nem felügyelt lemezek esetében a lemezre vonatkozó mérőszámok](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Következő lépések

- Az Azure Blob Storage által létrehozott naplók és mérőszámok ismertetését az [azure blob Storage monitorozási adatok referenciája](monitor-blob-storage-reference.md)című témakörben tekintheti meg.
- Az Azure-erőforrások monitorozásával kapcsolatos további információkért lásd: [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/essentials/monitor-azure-resource.md).
- A metrikák áttelepítésével kapcsolatos további információkért lásd: [Azure Storage-metrikák áttelepítése](../common/storage-metrics-migration.md).
