---
title: Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez
description: Küldjön Azure Monitor metrikákat és naplókat a Azure Monitor naplókba, az Azure Storage-ba vagy Azure Event Hubs diagnosztikai beállítással.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 60ac56cfda026871afa1725bbd54625b7ce7585e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789194"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez
[Az](./platform-logs-overview.md) Azure platformnaplói, beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azure-platformról, amelyektől függenek. [A platformmetrikákat](./data-platform-metrics.md) a rendszer alapértelmezés szerint gyűjti, és általában a metrika-Azure Monitor tárolja. Ez a cikk részletesen beosztja a diagnosztikai beállítások létrehozását és konfigurálását, hogy platformmetrikákat és platformnaplókat küldjön különböző célokra.

> [!IMPORTANT]
> Mielőtt létrehoz egy diagnosztikai beállítást a Tevékenységnaplóhoz, először tiltsa le az örökölt konfigurációkat. További [részletek: Örökölt](../essentials/activity-log.md#legacy-collection-methods) gyűjtemény metódusok.

Minden Azure-erőforráshoz saját diagnosztikai beállítás szükséges, amely a következő feltételeket határozza meg:

- A beállításban meghatározott célhelyre küldött napló- és metrikaadatok kategóriái. Az elérhető kategóriák eltérőek lehetnek a különböző erőforrástípusok esetében.
- Egy vagy több cél a naplók elküldését. A jelenlegi célok közé tartozik a Log Analytics-munkaterület, a Event Hubs és az Azure Storage.

Egyetlen diagnosztikai beállítás nem definiálhat egynél többet az egyes célhelyen. Ha egy adott céltípus (például két különböző Log Analytics-munkaterület) több típusának szeretne adatokat küldeni, akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállítással lehet.

Az alábbi videó végigvezeti a platformnaplók diagnosztikai beállításokkal való útválasztásán.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [A platformmetrikákat](./metrics-supported.md) a rendszer automatikusan elküldi [a Azure Monitor metrikáknak.](./data-platform-metrics.md) A diagnosztikai beállításokkal bizonyos Azure-szolgáltatások metrikákat küldhetnek Azure Monitor-naplókba [](../logs/log-query-overview.md) más monitorozási adatok elemzéséhez a naplólekérdezésekkel, bizonyos korlátozásokkal. 
>  
>  
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel. *Például:* Egy blokklánc IOReadBytes metrikáját csomópontszinten lehet feltárni és ábrázolni. Ha azonban diagnosztikai beállításokon keresztül exportálja, az exportált metrika az összes csomópont összes olvasási bájtját jelöli. Emellett a belső korlátozások miatt nem minden metrika exportálható Azure Monitor Log Analyticsbe. További információért tekintse meg [az exportálható metrikák listáját.](./metrics-supported-export-diagnostic-settings.md) 
>  
>  
> Az egyes metrikákra vonatkozó korlátozásokat úgy kerülheti meg, ha manuálisan kinyeri őket a [Metrics REST API](/rest/api/monitor/metrics/list) használatával, és importálja őket Azure Monitor Logsba a [Azure Monitor Data Collector API](../logs/data-collector-api.md)használatával.  


## <a name="destinations"></a>Célhelyek
A platformnaplókat és a metrikákat az alábbi táblázatban található célhelyre lehet küldeni. 

| Cél | Description |
|:---|:---|
| [Log Analytics-munkaterület](../logs/design-logs-deployment.md) | Ha naplókat és metrikákat küld egy Log Analytics-munkaterületre, elemezheti őket a Azure Monitor által gyűjtött egyéb monitorozási adatokkal, hatékony naplólekérdezésekkel, valamint más Azure Monitor-funkciók, például riasztások és vizualizációk használatával. |
| [Event Hubs](../../event-hubs/index.yml) | Ha naplókat és metrikákat küld a Event Hubs lehetővé teszi az adatok streamelését külső rendszerekbe, például külső SIEM-rendszerekbe és más naplóelemzési megoldásokba.  |
| [Azure Storage-fiók](../../storage/blobs/index.yml) | A naplók és metrikák Azure-tárfiókba való archiválása naplózáshoz, statikus elemzéshez vagy biztonsági mentéshez hasznos. A Azure Monitor és a Log Analytics-munkaterülethez képest az Azure Storage olcsóbb, és a naplók határozatlan ideig ott tarthatóak.  |


### <a name="destination-requirements"></a>Célkövetelmények

A diagnosztikai beállítások létrehozása előtt létre kell hoznunk a diagnosztikai beállítás célhelyét. A célhelynek nem kell ugyanabban az előfizetésben lennie, mint a naplókat küldő erőforrásnak, ha a beállítást konfiguráló felhasználó megfelelő Azure RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez. Az alábbi táblázat egyedi követelményeket tartalmaz minden célhoz, beleértve a regionális korlátozásokat is.

| Cél | Követelmények |
|:---|:---|
| Log Analytics-munkaterület | A munkaterületnek nem kell ugyanabban a régióban lennie, mint a figyelt erőforrásnak.|
| Event Hubs | A névtér megosztott hozzáférési szabályzata határozza meg a streamelési mechanizmus engedélyeit. A streamelés Event Hubs kezelés, küldés és figyelás engedélyekkel kell rendelkeznie. A diagnosztikai beállítás streamelésre való frissítéséhez ListKey engedéllyel kell rendelkeznie az adott engedélyezési Event Hubs számára.<br><br>Az eseményközpont-névtérnek ugyanabban a régióban kell lennie, mint a figyelt erőforrásnak, ha az erőforrás regionális. |
| Azure Storage-fiók | Ne használjon olyan meglévő tárfiókot, amely más, nem figyelési adatokat tárol benne, hogy jobban szabályozni tudja az adatokhoz való hozzáférést. Ha azonban a tevékenységnaplót és az erőforrásnaplókat együtt archiválja, dönthet úgy, hogy ugyanazt a tárfiókot használja az összes figyelési adat központi helyen való tárolásához.<br><br>Ha az adatokat nem módosítható tárolóba kell küldenie, állítsa be a tárfiók nem módosítható szabályzatát a Blob Storage nem módosíthatósági szabályzatának beállítása és [kezelése leírásában leírtak szerint.](../../storage/blobs/storage-blob-immutability-policies-manage.md) A cikkben található összes lépést végre kell követnie, beleértve a védett hozzáfűző blobok írásának engedélyezését is.<br><br>Ha az erőforrás regionális, a tárfióknak és a figyelt erőforrásnak ugyanabban a régióban kell lennie. |

> [!NOTE]
> Az Azure Data Lake Storage Gen2-fiókok jelenleg nem támogatottak a diagnosztikai beállítások célhelyeként, még akkor sem, ha az Azure Portalon esetleg érvényes lehetőségként vannak felsorolva.

> [!NOTE]
> Azure Monitor (Diagnosztikai beállítások) nem férnek hozzá Event Hubs erőforrásokhoz, ha a virtuális hálózatok engedélyezve vannak. Engedélyeznie kell, hogy a megbízható Microsoft-szolgáltatások megkerülje ezt a tűzfalbeállítást az Eseményközpontban, hogy az Azure Monitor (Diagnosztikai beállítások) szolgáltatás hozzáférjen a Event Hubs erőforrásokhoz. 


## <a name="create-in-azure-portal"></a>Létrehozás az Azure Portalon

A diagnosztikai beállításokat a Azure Portal konfigurálhatja a Azure Monitor menüből vagy az erőforrás menüjéből.

1. A diagnosztikai beállítások konfigurációja a Azure Portal az erőforrástól függ.

   - Egyetlen erőforráshoz kattintson a **Diagnosztikai** beállítások elemre az erőforrás menüjének **Figyelés** menüpontja alatt.

        ![Az erőforrásmenü Figyelés szakaszának képernyőképe a Azure Portal a Diagnosztikai beállítások kiemelésével.](media/diagnostic-settings/menu-resource.png)

   - Egy vagy több erőforráshoz kattintson  a Diagnosztikai beállítások elemre a Beállítások Azure Monitor menüben, majd kattintson az erőforrásra. 

        ![Képernyőkép a Menü menü Beállítások Azure Monitor a Diagnosztikai beállítások kiemelésével.](media/diagnostic-settings/menu-monitor.png)

   - A Tevékenységnapló menüben kattintson a Tevékenységnapló Azure Monitor **majd** a Diagnosztikai **beállítások elemre.**  Mindenképpen tiltsa le a tevékenységnapló örökölt konfigurációját. A [részletekért lásd:](./activity-log.md#legacy-collection-methods) Meglévő beállítások letiltása.

        ![Képernyőkép a Azure Monitor menüről, ahol a Tevékenységnapló és a Diagnosztikai beállítások ki van jelölve a Monitor-Activity menüsávon.](media/diagnostic-settings/menu-activity-log.png)

2. Ha a kiválasztott erőforráson nincsenek beállítások, a rendszer felkéri egy beállítás létrehozására. Kattintson **a Diagnosztikai beállítás hozzáadása elemre.**

   ![Diagnosztikai beállítás hozzáadása – nincs meglévő beállítás](media/diagnostic-settings/add-setting.png)

   Ha vannak meglévő beállítások az erőforráson, megjelenik a már konfigurált beállítások listája. Új **beállítás hozzáadásához kattintson** a Diagnosztikai beállítás hozzáadása lehetőségre, vagy a **Meglévő** szerkesztése lehetőségre. Minden beállításhoz nem lehet egynél több céltípus.

   ![Diagnosztikai beállítás hozzáadása – meglévő beállítások](media/diagnostic-settings/edit-setting.png)

3. Nevezze el a beállítást, ha még nem rendelkezik ilyen beállítással.

    ![Diagnosztikai beállítás hozzáadása](media/diagnostic-settings/setting-new-blank.png)

4. **Kategória részletei (mi az útvonal)** – Jelölje be az egyes, később a célhelyre küldeni kívánt adatkategóriák jelölőnégyzetét. A kategóriák listája az egyes Azure-szolgáltatások esetében eltérő lehet.

     - **Az AllMetrics** az erőforrás platformmetrikákat az Azure Logs-tárolóba, de napló formájában is átkérte. Ezeket a metrikákat általában csak a Azure Monitor idősorozat-adatbázisba küldi a rendszer. Ha elküldi őket a Azure Monitor-naplók tárolóba (amely a Log Analyticsen keresztül kereshető), akkor azokat más naplókban keresési lekérdezésekbe integrálhatja. Előfordulhat, hogy ez a lehetőség nem érhető el minden erőforrástípushoz. Ha támogatott, a [Azure Monitor metrikák](./metrics-supported.md) felsorolják, hogy milyen erőforrástípusokra vonatkozó metrikákat gyűjt a rendszer.

       > [!NOTE]
       > A metrikák naplókba való átirányításának Azure Monitor a cikk korábbi, korlátozásai.  


     - **A** naplók az erőforrástípustól függően különböző kategóriákat sorolnak fel. Ellenőrizze a célhelyre irányítható kategóriákat.

5. **Cél részletei** – Jelölje be az egyes célok jelölőnégyzetét. Ha be van pipva az egyes jelölőnégyzetek, a beállítások lehetővé teszik további információk hozzáadását.

      ![Küldés a Log Analyticsbe vagy Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – Adja meg az előfizetést és a munkaterületet.  Ha nem hoz létre munkaterületet, létre kell hoznia egyet, mielőtt [továbblépne.](../logs/quick-create-workspace.md)

    1. **Event Hubs** – Adja meg a következő feltételeket:
       - Az az előfizetés, amelynek az eseményközpont a része
       - Az Eseményközpont-névtér – Ha még nincs ilyene, létre kell [hoznia egyet](../../event-hubs/event-hubs-create.md)
       - Egy eseményközpont neve (nem kötelező), amelybe az összes adatot el kell küldeni. Ha nem ad meg nevet, a rendszer minden naplózási kategóriához létrehoz egy eseményközpontot. Ha több kategóriát is küld, megadhat egy nevet a létrehozott eseményközpontok számának korlátozásához. További [Azure Event Hubs a kvótákat és korlátokat.](../../event-hubs/event-hubs-quotas.md)
       - Eseményközpont-szabályzat (nem kötelező) A szabályzat határozza meg a streamelési mechanizmus engedélyeit. További információ: [Event-hubs-features.](../../event-hubs/event-hubs-features.md#publisher-policy)

    1. **Storage** – Válassza ki az előfizetést, a tárfiókot és a megőrzési szabályzatot.

        ![Küldés a Storage-be](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Érdemes lehet 0-ra állítani a megőrzési szabályzatot, és manuálisan törölni az adatokat a tárolóból egy ütemezett feladat használatával, hogy a jövőben elkerülhető legyen a félreértések elkerülése.
        >
        > Először is, ha tárterületet használ az archiváláshoz, általában több mint 365 napig szeretné használni az adatokat. Másodszor, ha 0-snál nagyobb megőrzési szabályzatot választ, a rendszer a lejárati dátumot csatolja a naplókhoz a tároláskor. A naplók dátumát a tárolás után nem módosíthatja.
        >
        > Ha például a *WorkflowRuntime* megőrzési szabályzatát 180 napra, majd 24 órával később 365 napra beállította, az ezen első 24 órában tárolt naplók 180 nap után automatikusan törlődnek, míg az ilyen típusú naplók 365 nap után automatikusan törlődnek. A megőrzési szabályzat későbbi módosításával a naplók első 24 órája nem marad meg 365 napig.

6. Kattintson a **Mentés** gombra.

Néhány pillanat múlva az új beállítás megjelenik az erőforrás beállításainak listájában, és a naplók az új eseményadatok létrehozása során a megadott célhelyre streamelnek. Egy esemény kibocsátása és a Log Analytics-munkaterületen való megjelenése között akár 15 perc is [eltelhet.](../logs/data-ingestion-time.md)

## <a name="create-using-powershell"></a>Létrehozás a PowerShell használatával

A [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmaggal hozzon létre egy diagnosztikai beállítást a [Azure PowerShell.](../powershell-samples.md) A paraméterek leírását a parancsmag dokumentációjában találhatja meg.

> [!IMPORTANT]
> Ez a módszer nem használható az Azure-tevékenységnaplóhoz. Ehelyett használja a Diagnosztikai beállítás létrehozása a Azure Monitor [sablonnal](./resource-manager-diagnostic-settings.md) Resource Manager sablont egy új Resource Manager létrehozásához és a PowerShell-sel való üzembe helyezéséhez.

Az alábbiakban egy PowerShell-parancsmagot mutatunk be, amely mindhárom célhely használatával létrehoz egy diagnosztikai beállítást.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Létrehozás az Azure CLI használatával

Az [az monitor diagnostic-settings create paranccsal](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) hozzon létre egy diagnosztikai beállítást az [Azure CLI-val.](/cli/azure/monitor) A paraméterek leírását a parancs dokumentációjában találhatja meg.

> [!IMPORTANT]
> Ez a módszer nem használható az Azure-tevékenységnaplóhoz. Ehelyett használja a Diagnosztikai beállítás létrehozása a Azure Monitor [sablonnal Resource Manager](./resource-manager-diagnostic-settings.md) sablont egy Resource Manager sablon létrehozásához és a CLI-val való üzembe helyezéséhez.

Az alábbiakban egy cli-példaparancsot mutatunk be egy diagnosztikai beállítás mindhárom cél használatával való létrehozásához. A szintaxis az ügyféltől függően némileg eltérő.

# <a name="cmd"></a>[Cmd](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Létrehozás Resource Manager sablon használatával
A [Resource Manager](./resource-manager-diagnostic-settings.md) sablonnal való létrehozására vagy frissítésére vonatkozó diagnosztikai Azure Monitor sablonmintákat a Resource Manager meg.

## <a name="create-using-rest-api"></a>Létrehozás a REST API használatával
A [diagnosztikai beállítások](/rest/api/monitor/diagnosticsettings) létrehozásához vagy frissítéséhez lásd: Diagnosztikai beállítások az [Azure Monitor REST API.](/rest/api/monitor/)

## <a name="create-using-azure-policy"></a>Létrehozás Azure Policy
Mivel minden Azure-erőforráshoz létre kell hozni egy diagnosztikai beállítást, Azure Policy automatikusan létrehozhat egy diagnosztikai beállítást az egyes erőforrások létrehozásakor. A [részleteket Azure Monitor nagy léptékű üzembe helyezési Azure Policy](../deploy-scale.md) használatával.

## <a name="metric-category-is-not-supported-error"></a>A metrikakategória nem támogatott hiba
Diagnosztikai beállítások telepítésekor a következő hibaüzenet jelenik meg:

   "A *(xxxx)* metrikakategória nem támogatott"

Például: 

   "Az ActionsFailed metrikakategória nem támogatott"

ahol korábban az üzembe helyezés sikeres volt. 

A probléma akkor fordul elő, Resource Manager sablont használ, a diagnosztikai beállításokat REST API, az Azure CLI-t vagy Azure PowerShell. A rendszer nem érinti a Azure Portal által létrehozott diagnosztikai beállításokat, mivel csak a támogatott kategórianevek jelennek meg.

A problémát a mögöttes API közelmúltbeli módosítása okozza. Az "AllMetrics" kivételével a metrikakategóriák nem támogatottak, és csak néhány nagyon specifikus Azure-szolgáltatás esetében voltak támogatottak. Korábban a rendszer figyelmen kívül hagyta az egyéb kategórianeveket a diagnosztikai beállítások telepítésekor. A Azure Monitor háttér egyszerűen átirányította ezeket a kategóriákat az "AllMetrics" (AllMetrics) kategóriába.  2021 februárjától a háttért frissítették, hogy pontosabban ellenőrizze, hogy a megadott metrikakategória pontos-e. Ez a módosítás egyes üzembe helyezések meghiúsultát okozta.

Ha ezt a hibaüzenetet kapja, frissítse az üzemelő példányokat úgy, hogy a metrikakategóriák neveit az "AllMetrics" névre cserélve javítsa ki a problémát. Ha az üzemelő példány korábban több kategóriát is hozzáadott, csak egyet kell az "AllMetrics" referenciával együtt tartani. Ha a probléma továbbra is jelentkezik, lépjen kapcsolatba a Azure-támogatás a Azure Portal. 



## <a name="next-steps"></a>Következő lépések

- [További információ az Azure platform naplóiról](./platform-logs-overview.md)
