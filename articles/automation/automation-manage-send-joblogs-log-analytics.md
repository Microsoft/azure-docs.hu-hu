---
title: Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé
description: Ez a cikk azt mutatja be, hogyan küldhet feladatállapotot és runbook-feladatstreameket a Azure Monitor naplókba.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c24c38368ef20dadd0dc19b1804f9d27ad68bd27
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833687"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé

Azure Automation runbook-feladat állapotát és feladatstreameket küldhet a Log Analytics-munkaterületre. Ez a folyamat nem jár munkaterület-összekapcsolással, és teljesen független. A feladatnaplók és a feladatstreamek az egyes Azure Portal a PowerShellben láthatók, és ez lehetővé teszi egyszerű vizsgálatok elvégzését. A naplók Azure Monitor a következőt használhatja:

* Betekintést nyerhet az Automation-feladatok állapotába.
* E-mail vagy riasztás aktiválása a Runbook-feladat állapota alapján (például sikertelen vagy felfüggesztett).
* Speciális lekérdezéseket írhat a feladatstreamekben.
* Feladatok korrelálása Automation-fiókok között.
* Egyéni nézetek és keresési lekérdezések használatával vizualizálhatja a runbook eredményeit, a runbook feladatának állapotát és egyéb kapcsolódó fő mutatókat vagy metrikákat.

## <a name="prerequisites"></a>Előfeltételek

Az Automation-naplók a naplókba való Azure Monitor a következőre lesz szüksége:

* Az Azure PowerShell legújabb [kiadása.](/powershell/azure/)

* Egy Log Analytics-munkaterület és annak erőforrás-azonosítója. További információ: Get started with Azure Monitor logs (A [naplók Azure Monitor első lépések).](../azure-monitor/overview.md)

* Az Azure Automation erőforrás-azonosítója.

## <a name="how-to-find-resource-ids"></a>Erőforrás-hozzárendelések megkeresása

1. A következő paranccsal keresse meg az erőforrás-azonosítót a Azure Automation fiókjához:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Másolja ki a **ResourceID értékét.**

3. Az alábbi paranccsal keresse meg a Log Analytics-munkaterület erőforrás-azonosítóját:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Másolja ki a **ResourceID értékét.**

Egy adott erőforráscsoport eredményeinek visszaadása érdekében adja meg a `-ResourceGroupName` paramétert. További információ: [Get-AzResource.](/powershell/module/az.resources/get-azresource)

Ha egynél több Automation-fiókkal vagy -munkaterülettel rendelkezik az előző parancsok kimenetében, a nevet és az egyéb kapcsolódó tulajdonságokat, amelyek az Automation-fiók teljes erőforrás-azonosítójának részét képezi, a következőt kell elvégeznie:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal Automation-fiókját az **Automation-fiókok lapon.**
1. A kiválasztott Automation-fiók oldalán, a **Fiókbeállítások alatt válassza** a Tulajdonságok **lehetőséget.**
1. Jegyezze **fel** az alább látható részleteket a Tulajdonságok lapon.

    ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

Az Automation diagnosztikai beállításai a következő platformnaplók és metrikaadatok továbbítását támogatják:

* JobLogs (Feladatnaplók)
* JobStreams (Feladatfolyamok)
* DSCNodeStatus
* Metrikák – Összes feladat, frissítéstelepítési gépek teljes futtatása, frissítéstelepítések teljes futtatása

Az Automation-naplók a naplókba Azure Monitor [](../azure-monitor/essentials/diagnostic-settings.md) a diagnosztikai beállítások létrehozása szakaszt, amelyből megértheti a platformnaplók küldésére vonatkozó diagnosztikai beállítások konfigurálható funkcióját és módszereit.

## <a name="azure-monitor-log-records"></a>Azure Monitor naplórekordok létrehozása

Azure Automation diagnosztikák két rekordtípust hoznak létre a Azure Monitor naplókban, a címkével `AzureDiagnostics` megjelölve. A következő szakaszokban lévő táblák olyan rekordokra mutatnak példákat, Azure Automation a naplókeresési eredményekben megjelenő adattípusokat.

### <a name="job-logs"></a>Feladatnaplók

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívó bérlőét azonosító GUID. |
| JobId_g |A runbook-feladatot azonosító GUID. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>- Új<br>– Létrehozva<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Befejezve |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az Automation értéke Job. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | A naplókat Azure Monitor rendszer gyűjti az adatokat. Az érték mindig Azure az Azure-diagnosztika számára. |
| ResultDescription |A runbook-feladat eredményállapota. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |A runbook-feladat korrelációs GUID-ja. |
| ResourceId |A Azure Automation runbook fiókerőforrás-azonosítóját. |
| SubscriptionId | Az Automation-fiók Azure-előfizetésének GUID-ja. |
| ResourceGroup | Az Automation-fiók erőforráscsoportja neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték MICROSOFT. Automatizálás. |
| ResourceType | Az erőforrás típusa. Az érték AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Feladatstreamek
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívó bérlőét azonosító GUID. |
| JobId_g |A runbook-feladatot azonosító GUID. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>- Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az Automation értéke Job. |
| Erőforrás | Az Automation-fiók neve. |
| SourceSystem | A naplókat Azure Monitor rendszer gyűjti az adatokat. Az érték mindig Azure az Azure-diagnosztika számára. |
| ResultDescription |Leírás, amely tartalmazza a runbook kimeneti adatfolyamát. |
| CorrelationId |A runbook-feladat korrelációs GUID-ja. |
| ResourceId |A Azure Automation fiók erőforrás-azonosítója. |
| SubscriptionId | Az Automation-fiók Azure-előfizetésének GUID-ja. |
| ResourceGroup | Az Automation-fiók erőforráscsoportja neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték MICROSOFT. Automatizálás. |
| ResourceType | Az erőforrás típusa. Az érték AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Automation-naplók megtekintése Azure Monitor naplókban

Most, hogy megkezdte az Automation-feladatstreamek és -naplók küldését az Azure Monitor-naplókba, lássuk, hogyan használhatja ezeket a naplókat a Azure Monitor naplókban.

A naplók megtekintéséhez futtassa a következő lekérdezést: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mail küldése, ha egy Runbook-feladat meghiúsul vagy felfüggeszti

A következő lépések azt mutatják be, hogyan állíthat be riasztásokat a Azure Monitor, hogy értesítse Önt, ha hiba történik egy Runbook-feladatban.

Riasztási szabály létrehozásához először hozzon létre naplókeresést a runbook feladatrekordjaihoz, amelyekre a riasztást kell meghívni. Kattintson **a Riasztás gombra** a riasztási szabály létrehozásához és konfiguráláshoz.

1. A Log Analytics-munkaterület Áttekintés lapján kattintson a **Naplók megtekintése elemre.**

2. Hozzon létre egy naplókeresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>A runbook neve alapján is csoportosíthat a következő használatával: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha egynél több Automation-fiókból vagy -előfizetésből hoz létre naplókat a munkaterületre, a riasztásokat előfizetés és Automation-fiók szerint csoportosíthatja. Az Automation-fiók neve a keresőmezőben `Resource` `JobLogs` található.

3. A Szabály **létrehozása képernyő megnyitásához** kattintson a lap tetején található **Új** riasztási szabály elemre. A riasztás konfigurálható beállításaival kapcsolatos további információkért lásd: Naplóriasztás [az Azure-ban.](../azure-monitor/alerts/alerts-unified-log.md)

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Az összes hibával befejezett feladat megkeresása

A hibákra vonatkozó riasztások mellett azt is megtalálja, ha egy Runbook-feladat megszakítást nem hozó hibával rendelkezik. Ezekben az esetekben a PowerShell hibastreamet hoz létre, de a megszakítás nélküli hibák nem okják a feladat felfüggesztését vagy meghiúsulését.

1. A Log Analytics-munkaterületen kattintson a Naplók **elemre.**

2. A lekérdezés mezőbe írja be a következőt: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Kattintson a **Keresés gombra.**

### <a name="view-job-streams-for-a-job"></a>Feladat adatfolyamának megtekintése

Amikor hibakeresést végez egy feladaton, érdemes lehet a feladatstreameket is átfutni. A következő lekérdezés egy GUID azonosítóval végzett feladat összes streamét `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` megjeleníti:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Feladat előzményállapotának megtekintése

Végül érdemes lehet a feladatelőzményeket az idő során vizualizálni. Ezzel a lekérdezéssel keresheti meg a feladatok állapotát az idő alatt.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics korábbi feladatállapot-diagramja](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Feladatállapot kimenetének szűrése JSON-objektumra konvertálva

Nemrég módosítottuk az Automation-naplóadatok Log Analytics-szolgáltatás táblába való írásának viselkedését, ahol már nem külön mezőkre bontják a `AzureDiagnostics` JSON-tulajdonságokat. Ha úgy konfigurálta a runbookot, hogy a kimeneti streamben lévő objektumokat JSON formátumban, különálló oszlopokként formázza, akkor a lekérdezéseket újra kell konfigurálni, hogy a mező JSON-objektummal elemezze a mezőt a tulajdonságok eléréséhez. Ez a [parsejson használatával](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) valósítja meg egy ismert elérési út egy adott JSON-elemét.

Egy runbook például több mezővel formázza a *resultDescription* tulajdonságot a kimeneti streamben JSON formátumban. Az Állapot mezőben megadott sikertelen állapotú feladatok állapotának kereséséhez használja ezt a példalekérdezést a *ResultDescription* lekérdezés Sikertelen állapotú **kereséséhez:**

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics korábbi feladatstream JSON-formátuma](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Következő lépések

* A keresési lekérdezések felépítésével és az Automation-feladatnaplók áttekintését Azure Monitor Naplókeresések a [naplókban Azure Monitor meg.](../azure-monitor/logs/log-query-overview.md)
* A runbookok kimeneti és hibaüzenetei létrehozásának és lekérésének a forgatókönyvből való létrehozásával és lekérését lásd: [Runbook kimenetének figyelése.](automation-runbook-output-and-messages.md)
* A runbook végrehajtásával, a runbook-feladatok figyelése és egyéb technikai részletekkel kapcsolatos további információkért lásd: Runbook végrehajtása a [Azure Automation.](automation-runbook-execution.md)
* A naplók és adatgyűjtési források Azure Monitor további információért lásd: Azure Storage-adatok gyűjtése a [naplókban Azure Monitor áttekintése.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
* A Log Analytics hibaelhárításával kapcsolatos segítségért lásd: A Log Analytics által már nem gyűjtött [adatok hibaelhárítása.](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)
