---
title: Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé
description: Ez a cikk azt ismerteti, hogyan lehet elküldeni a feladatok állapotát és a runbook Azure Monitor naplókba.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 4199c5576662eee1dd6cedc388440a71e21f8b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581206"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé

A Azure Automation a runbook feladatok állapotát és a feladatok streamjét a Log Analytics munkaterületre küldheti. Ez a folyamat nem jár a munkaterület összekapcsolásával, és teljesen független. A feladatok naplói és a feladatok adatfolyamai láthatók a Azure Portalban, vagy a PowerShell-lel az egyes feladatokhoz, és ez lehetővé teszi az egyszerű vizsgálatok végrehajtását. A Azure Monitor naplók mostantól a következőket teheti:

* Betekintést nyerhet az Automation-feladatok állapotával.
* E-mail vagy riasztás elindítása a runbook-feladatok állapota (például sikertelen vagy felfüggesztett) alapján.
* Speciális lekérdezések írása a feladatok adatfolyamai között.
* A feladatok összekapcsolása az Automation-fiókok között.
* Egyéni nézeteket és keresési lekérdezéseket használhat a runbook eredményeinek, a runbook-feladatok állapotának, valamint az egyéb kapcsolódó kulcsfontosságú mutatók vagy mérőszámok megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az Automation-naplók Azure Monitor naplókba való küldésének megkezdéséhez a következőkre lesz szüksége:

* [Azure PowerShell](/powershell/azure/)legújabb kiadása.

* Egy Log Analytics munkaterület és erőforrás-azonosítója. További információ: Ismerkedés [a Azure monitor-naplókkal](../azure-monitor/overview.md).

* Az Azure Automation-fiók erőforrás-azonosítója.

## <a name="how-to-find-resource-ids"></a>Erőforrás-azonosítók megkeresése

1. Használja az alábbi parancsot a Azure Automation-fiók erőforrás-AZONOSÍTÓjának megkereséséhez:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Másolja a **ResourceId** értékét.

3. A Log Analytics munkaterület erőforrás-AZONOSÍTÓjának megkereséséhez használja a következő parancsot:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Másolja a **ResourceId** értékét.

Egy adott erőforráscsoport eredményeinek visszaadásához adja meg a `-ResourceGroupName` paramétert. További információ: [Get-AzResource](/powershell/module/az.resources/get-azresource).

Ha több Automation-fiókkal vagy-munkaterülettel rendelkezik az előző parancsok kimenetében, megkeresheti az Automation-fiók teljes erőforrás-AZONOSÍTÓjának részét képező nevet és az egyéb kapcsolódó tulajdonságokat a következők végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal válassza ki az Automation-fiókját az **Automation-fiókok** oldalon.
1. A kiválasztott Automation-fiók lapjának **Fiókbeállítások** területén válassza a **Tulajdonságok** lehetőséget.
1. A **Tulajdonságok** lapon jegyezze fel az alább látható adatokat.

    ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

Az Automation diagnosztikai beállításai támogatják a következő platform-naplók és mérőszám-adatok továbbítását:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metrikák – összes feladat, összes frissítés központi telepítési gépe, összes frissítés központi telepítésének futtatása

Az Automation-naplók Azure Monitor naplókba való küldésének megkezdéséhez tekintse át a [diagnosztikai beállítások létrehozása](../azure-monitor/essentials/diagnostic-settings.md) című témakört, amelyből megismerheti a platform-naplók küldéséhez szükséges diagnosztikai beállításokat.

## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor

Azure Automation diagnosztika két típusú rekordot hoz létre a Azure Monitor naplókban, címkézve `AzureDiagnostics` . A következő részekben szereplő táblázatok a Azure Automation által generált és a naplók keresési eredményei között megjelenő adattípusokra vonatkozó példák.

### <a name="job-logs"></a>Feladatnaplók

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| JobId_g |A runbook-feladatot azonosító GUID. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Új<br>-Létrehozva<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>-Befejezett |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az Automation esetében az érték a job. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | A rendszer, amely Azure Monitor naplózza az adatok gyűjtését. Az érték mindig Azure az Azure Diagnostics szolgáltatáshoz. |
| ResultDescription |A runbook-feladatok eredményének állapota. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |A runbook-feladatokhoz tartozó korrelációs GUID |
| ResourceId |A runbook Azure Automation fiókjának erőforrás-azonosítója. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés GUID-azonosítója. |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték a MICROSOFT. Automation. |
| ResourceType | Az erőforrás típusa. Az érték a AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Feladatstreamek
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| JobId_g |A runbook-feladatot azonosító GUID. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az Automation esetében az érték a job. |
| Erőforrás | Az Automation-fiók neve. |
| SourceSystem | A rendszer, amely Azure Monitor naplózza az adatok gyűjtését. Az érték mindig Azure az Azure Diagnostics szolgáltatáshoz. |
| ResultDescription |A runbook kimeneti streamjét tartalmazó leírás. |
| CorrelationId |A runbook-feladatokhoz tartozó korrelációs GUID |
| ResourceId |A runbook Azure Automation fiókjának erőforrás-azonosítója. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés GUID-azonosítója. |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték a MICROSOFT. Automation. |
| ResourceType | Az erőforrás típusa. Az érték a AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Azure Monitor naplókban található Automation-naplók megtekintése

Most, hogy elindította az Automation-feladatok folyamait és naplóit Azure Monitor naplókba, lássuk, mit tehet a naplókban Azure Monitor naplókon belül.

A naplók megtekintéséhez futtassa a következő lekérdezést: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mail küldése runbook-feladatok sikertelensége vagy felfüggesztése esetén

A következő lépések bemutatják, hogyan állíthatja be a riasztásokat a Azure Monitorban, hogy értesítést kapjon, ha valami probléma merül fel egy runbook-feladatokkal.

Riasztási szabály létrehozásához először hozzon létre egy naplót a runbook-feladatokhoz, amelyeknek meg kell hívniuk a riasztást. A riasztási szabály létrehozásához és konfigurálásához kattintson a **riasztás** gombra.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók megtekintése** elemre.

2. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>A runbook neve a használatával is csoportosítható: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha több Automation-fiókból vagy-előfizetésből állít be naplókat a munkaterületre, a riasztásokat az előfizetés és az Automation-fiók alapján csoportosíthatja. Az Automation-fiók neve a `Resource` Keresés mezőjében található `JobLogs` .

3. A **szabály létrehozása** képernyő megnyitásához kattintson az oldal tetején található **új riasztási szabály** elemre. A riasztás konfigurálásával kapcsolatos további információkért lásd: [riasztások naplózása az Azure-ban](../azure-monitor/alerts/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>A hibákkal befejezett összes feladat megkeresése

A hibákkal kapcsolatos riasztások mellett megkeresheti, hogy egy runbook-feladatokhoz ne kelljen megszakítást okozó hiba. Ezekben az esetekben a PowerShell egy hibát okozó adatfolyamot hoz létre, de a megszakítás nélküli hibák miatt a feladat felfüggesztése vagy meghibásodása sikertelen lesz.

1. A Log Analytics munkaterületen kattintson a **naplók** elemre.

2. A lekérdezés mezőbe írja be a következőt: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Kattintson a **Keresés** gombra.

### <a name="view-job-streams-for-a-job"></a>Feladatokhoz tartozó feladatok adatfolyamának megtekintése

A feladatok hibakeresése során érdemes megtekinteni a feladatok folyamait is. A következő lekérdezés a GUID azonosítóval rendelkező egyetlen feladatokhoz tartozó összes streamet megjeleníti `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Korábbi feladatok állapotának megtekintése

Végezetül érdemes lehet megjeleníteni a feladatok előzményeit az idő múlásával. Ezt a lekérdezést használhatja a feladatok állapotának megkeresésére az idő múlásával.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics korábbi feladatok állapotának diagramja](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>A feladatok állapotának szűrése egy JSON-objektumba

A közelmúltban módosítottuk az Automation-naplóban lévő adatnak a Log Analytics szolgáltatásban található táblába való beírásának viselkedését `AzureDiagnostics` , ahol a JSON-tulajdonságokat a továbbiakban nem bontja külön mezőkre. Ha úgy konfigurálta a runbook, hogy az objektumokat JSON formátumban formázza a kimeneti adatfolyamban, akkor a tulajdonságok eléréséhez újra kell konfigurálnia a lekérdezéseket, hogy az adott mezőt egy JSON-objektumba elemezze. Ezt a [parseJSON](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) használatával lehet elérni egy ismert elérési úton található JSON-elem eléréséhez.

Egy runbook például a *ResultDescription* tulajdonságot a kimeneti adatfolyamban, JSON formátumban formázza több mezővel. Ha olyan feladatok állapotát szeretné megkeresni, amelyek **állapota "állapot**" nevű mezőben van megadva, akkor a példában szereplő lekérdezéssel a **sikertelen** állapotú *ResultDescription* keresheti meg a következőt:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics korábbi feladatok stream JSON-formátuma](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan hozhat létre keresési lekérdezéseket, és hogyan tekintheti át az Automation-feladatok naplóit Azure Monitor-naplókkal, tekintse meg a [Azure monitor](../azure-monitor/logs/log-query-overview.md)naplókban végzett
* A runbookok származó kimeneti és hibaüzenetek létrehozásának és lekérésének megismeréséhez lásd: [runbook-kimenet figyelése](automation-runbook-output-and-messages.md).
* A runbook végrehajtásával, a runbook-feladatok figyelésével és egyéb technikai részletekkel kapcsolatos további tudnivalókért tekintse meg [a runbook végrehajtásáról szóló részt Azure Automation](automation-runbook-execution.md).
* Ha többet szeretne megtudni Azure Monitor a naplókról és az adatgyűjtési forrásokról, tekintse meg [Az Azure Storage-adatok gyűjtése Azure monitor naplók áttekintését](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)ismertető cikket.
* A Log Analytics hibaelhárításával kapcsolatban lásd: [Hibaelhárítás, hogy a log Analytics miért nem gyűjti az adatgyűjtést](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).