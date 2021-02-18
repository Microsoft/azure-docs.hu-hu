---
title: Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával | Microsoft Docs
description: A naplók riasztási szabályainak létrehozása, megtekintése és kezelése Azure Monitor használatával
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 579e86df23a0b6369f7ff5b6113c4972e054d5cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620965"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával

## <a name="overview"></a>Áttekintés

A riasztások naplózása lehetővé teszi, hogy a felhasználók egy [log Analytics](../log-query/log-analytics-tutorial.md) lekérdezést használjanak, amellyel kiértékelheti az erőforrások összes készletének gyakoriságát, és egy riasztást az eredmények alapján. A szabályok egy vagy több műveletet aktiválnak a [műveleti csoportok](../platform/action-groups.md)használatával. [További információ a naplózási riasztások funkcióival és terminológiával kapcsolatban](../platform/alerts-unified-log.md).

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet naplózási riasztásokat a Azure Monitor használatával. A riasztási szabályokat három összetevő határozza meg:
- Cél: A figyelni kívánt Azure-erőforrás.
- Feltételek: a kiértékeléshez használandó logika. Ha teljesülnek, a riasztás tüzet vált.  
- Művelet: értesítések vagy Automation – e-mail, SMS, webhook stb.

A naplózási riasztási szabályokat Azure Resource Manager sablonok használatával is létrehozhatja, amelyek [külön cikkben](../alerts/alerts-log-create-templates.md)vannak leírva.

> [!NOTE]
> [Log Analytics munkaterületről](../log-query/log-analytics-tutorial.md) származó adatok naplózása elküldhető a Azure monitor metrikák tárolójába. A metrikák riasztásai [eltérő viselkedéssel](../platform/alerts-metric-overview.md)rendelkeznek, ami kívánatosabb lehet a használt adatoktól függően. A naplók mérőszámokra való átirányításával kapcsolatos információkért lásd: [metrika riasztás a naplókhoz](../platform/alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Napló-riasztási szabály létrehozása a Azure Portal

Itt találja a riasztási lekérdezések írásához szükséges lépéseket:

1. Keresse meg azt az erőforrást, amelyre a riasztást szeretné. Ha lehetséges, érdemes lehet több erőforráson beállítania a riasztási szabályokat az előfizetés vagy az erőforráscsoport hatókörének kiválasztásával. Több erőforrás riasztása csökkenti a költségeket, és több riasztási szabályt kell kezelnie.
1. A **figyelés** területen válassza a **naplók** lehetőséget.
1. Kérdezze le a naplóadatokat, amelyek a hibát jelezhetik. A [riasztási lekérdezés példái című témakörből](../log-query/example-queries.md) megtudhatja, hogy milyen módon derítheti fel vagy kezdheti [meg a saját lekérdezésének megírását](../log-query/log-analytics-tutorial.md). Ezenkívül [megtudhatja, hogyan hozhat létre optimalizált riasztási lekérdezéseket](alerts-log-query.md).
1. Nyomja meg az "+ új riasztási szabály" gombot a riasztás létrehozási folyamatának elindításához.

    ![Log Analytics riasztás beállítása](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Azt javasoljuk, hogy a riasztásokat nagy léptékben hozza létre, amikor erőforrás-hozzáférési módot használ a naplókhoz, amely egy erőforráscsoport vagy előfizetési hatókör használatával több erőforráson fut. A nagyméretű riasztások csökkentik a szabályok felügyeletének terhelését. Az erőforrások célzásához adja meg az erőforrás-azonosító oszlopot az eredmények között. [További információ a riasztások dimenziók szerinti felosztásáról](../platform/alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Log Analytics és Application Insights naplózási riasztása

1. Ha a lekérdezés szintaxisa helyes, a lekérdezéshez tartozó korábbi adatok diagramként jelennek meg a diagram időszakának az elmúlt hat órában a múlt héten való megcsípése lehetőségével.
 
    Ha a lekérdezés eredménye összefoglaló vagy [projekt](/azure/kusto/query/projectoperator) -specifikus oszlopokat tartalmaz a Time oszlop nélkül, akkor a diagram egyetlen értéket jelenít meg.

    ![Riasztási szabály konfigurálása](media/alerts-log/AlertsPreviewAlertLog.png)

1. Válassza ki azt az időtartományt, amelyen a megadott feltételt az [**időtartam**](../platform/alerts-unified-log.md#query-time-range) lehetőség használatával értékelheti.

1. A naplózási riasztások két típusú [**mértéken**](../platform/alerts-unified-log.md#measure)alapulnak:
    1. **Eredmények száma** – a lekérdezés által visszaadott rekordok száma.
    1. **Metrika mérése**  -  *Összesítési érték* kiszámítva a kiválasztott kifejezések és a [bin ()](/azure/kusto/query/binfunction) kiválasztásával csoportosított összesítéssel. Például:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. A metrikai mérések riasztási logikája esetében megadhatja, hogy a rendszer hogyan [Ossza szét a riasztásokat dimenziók](../platform/alerts-unified-log.md#split-by-alert-dimensions) alapján az **Összesítés** lehetőség használatával. A sorok csoportosítási kifejezésének egyedinek és rendezettnek kell lennie.

    > [!NOTE]
    > Mivel a [bin ()](/azure/kusto/query/binfunction) nem egyenletes időintervallumot eredményezhet, a riasztási szolgáltatás automatikusan átalakítja a [bin ()](/azure/kusto/query/binfunction) függvényt, hogy [bin_at ()](/azure/kusto/query/binatfunction) függvényt a megfelelő időpontra konvertálja, hogy az eredmények egy rögzített ponttal legyenek biztosítva.

    > [!NOTE]
    > A riasztási dimenziók felosztása csak az aktuális scheduledQueryRules API esetében érhető el. Ha az örökölt [log Analytics riasztási API](../platform/api-alerts.md)-t használja, váltania kell. [További információ a váltásról](./alerts-log-api-switch.md). Az erőforrás-központú riasztások méretezése csak az API-verzióban `2020-05-01-preview` és a fentiekben támogatott.

    ![Összesítés lehetőség szerint](media/alerts-log/aggregate-on.png)

1. Ezután az előnézeti adathalmaz alapján állítsa be az [ **operátort**, a **küszöbértéket**](../platform/alerts-unified-log.md#threshold-and-operator)és a [**gyakoriságot**](../platform/alerts-unified-log.md#frequency).

1. Megadhatja azt is, [hogy a szabálysértések száma](../platform/alerts-unified-log.md#number-of-violations-to-trigger-alert) **teljes vagy egymást követő szabálysértések** használatával történjen.

1. Válassza a **Kész** lehetőséget. 

1. Adja meg a **riasztási szabály nevét**, **leírását**, és válassza ki a riasztás **súlyosságát**. Ezeket a részleteket minden riasztási műveletben használják. Emellett dönthet úgy is, hogy nem aktiválja a riasztási szabályt a létrehozáshoz a **szabály engedélyezése a létrehozáskor** lehetőség kiválasztásával.

1. Válassza ki, hogy szeretné-e letiltani a szabályok műveleteit egy riasztás elindítását követően, majd használja a [**riasztások mellőzése**](../platform/alerts-unified-log.md#state-and-resolving-alerts) beállítást. A szabály továbbra is futni fog, és riasztásokat hoz létre, de a rendszer nem indít el műveleteket a zaj megelőzése érdekében. Az Elnémítás művelet értékének nagyobbnak kell lennie, mint a riasztások hatékonyságának gyakorisága.

    ![Riasztások mellőzése a naplózási riasztásokhoz](media/alerts-log/AlertsPreviewSuppress.png)

1. Annak megadása, hogy a riasztási szabály egy vagy több [**műveleti csoportot**](../platform/action-groups.md#webhook) indítson el, ha a riasztási feltétel teljesül.

    > [!NOTE]
    > Tekintse meg az [Azure-előfizetési szolgáltatás](../../azure-resource-manager/management/azure-subscription-service-limits.md) korlátozásait az elvégezhető műveletek korlátain.  

1. A naplózási riasztási szabályokban a műveleteket igény szerint testreszabhatja:

    - **Egyéni E-mail tárgya**: felülbírálja az e-mail-műveletek *tárgyát* . Nem módosíthatja a levél törzsét, és ez a mező **nem használható e-mail-címekhez**.
    - **Egyéni JSON-adattartalom belefoglalása**: felülbírálja a műveleti csoportok által használt WEBhook JSON-t, feltételezve, hogy a műveleti csoport webhook műveletet tartalmaz. További információ a [naplóbeli riasztások webhook műveletéről](./alerts-log-webhook.md).

    ![Műveletek felülbírálásai a naplók Értesítéseinél](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Ha az összes mező helyesen van beállítva, a **riasztási szabály létrehozása** gomb rákattinthat, és létrejön egy riasztás.

    Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

    ![Szabály létrehozása](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>A riasztások kezelése Log Analytics és Application Insights naplózási riasztás létrehozása

> [!NOTE]
> A riasztások kezelése szolgáltatásból való létrehozás jelenleg nem támogatott az erőforrás-központú naplók esetében

1. A [portálon](https://portal.azure.com/)válassza a **figyelés** , majd a **riasztások** lehetőséget.

    ![Figyelés](media/alerts-log/AlertsPreviewMenu.png)

1. Válassza az **új riasztási szabály** lehetőséget. 

    ![Riasztás hozzáadása](media/alerts-log/AlertsPreviewOption.png)

1. Megjelenik a **riasztás létrehozása** panel. Négy részből áll: 
    - Az erőforrás, amelyre a riasztás vonatkozik.
    - Az ellenőrzési feltétel.
    - A feltétel teljesülése esetén végrehajtandó műveletek.
    - A riasztások nevének és leírásának részletei. 

    ![Szabály létrehozása](media/alerts-log/AlertsPreviewAdd.png)

1. Nyomja meg az **erőforrás kiválasztása** gombot. A szűréshez válassza ki az *előfizetést*, az *erőforrás típusát*, majd válasszon ki egy erőforrást. Győződjön meg arról, hogy az erőforráshoz napló áll rendelkezésre.

   ![Erőforrás kiválasztása](media/alerts-log/Alert-SelectResourceLog.png)

1. Ezután a **feltétel** hozzáadása gomb használatával megtekintheti az erőforráshoz rendelkezésre álló jel-beállítások listáját. Válassza az **egyéni naplók keresése** lehetőséget.

   ![Erőforrás kiválasztása – egyéni naplók keresése](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > A riasztások portál felsorolja a Log Analytics és Application Insights mentett lekérdezéseit, amelyek sablon-riasztási lekérdezésként is használhatók.

1. Ha kijelöli, a **keresési lekérdezés** mezőben megírhatja, beillesztheti vagy szerkesztheti a riasztási lekérdezést.

1. Folytassa az [előző szakaszban](#log-alert-for-log-analytics-and-application-insights)ismertetett következő lépésekkel.

### <a name="log-alert-for-all-other-resource-types"></a>Napló riasztása minden más erőforrástípus esetében

> [!NOTE]
> Az API `2020-05-01-preview` -verzió és az erőforrás-központú naplózási riasztások esetében jelenleg nem számítunk fel további díjakat.  Az előzetes verzióban elérhető szolgáltatások díjszabása a jövőben lesz bejelentve, és a számlázás megkezdése előtt értesítést küld. Ha továbbra is az új API-verziót és az erőforrás-központú napló riasztásait szeretné használni a hirdetmény lejárta után, akkor a megfelelő díjszabás alapján számítjuk fel a díjat.

1. Kezdés a **feltétel** lapról:

    1. Győződjön meg arról, hogy a [**mérték**](../platform/alerts-unified-log.md#measure), az [**összesítés típusa**](../platform/alerts-unified-log.md#aggregation-type)és az [**Összesítés részletessége**](../platform/alerts-unified-log.md#aggregation-granularity) helyes. 
        1. Alapértelmezés szerint a szabály az elmúlt 5 percben megszámolja az eredmények számát.
        1. Ha az összesített lekérdezési eredményeket érzékeljük, a rendszer néhány másodpercen belül automatikusan frissíti a szabályt, hogy rögzítse azt.

    1. Ha szükséges, válassza [a riasztások felosztása dimenzió alapján](../platform/alerts-unified-log.md#split-by-alert-dimensions)lehetőséget: 
       - A rendszer automatikusan kijelöli az **erőforrás-azonosító oszlopot** , ha az észlelve van, és a felgyújtott riasztás környezetét módosítja a rekord erőforrására. 
       - Az **erőforrás-azonosító oszlop** kiválasztható a riasztások kiválasztásához az előfizetésen vagy az erőforráscsoportokon. A kiválasztás akkor hasznos, ha a lekérdezés eredményei több erőforráson alapulnak. Például egy olyan lekérdezés, amely azt vizsgálja, hogy az erőforráscsoport virtuális gépei 80%-a magas CPU-használatot tapasztal-e.
       - Akár hat további felosztás is kiválasztható tetszőleges számú vagy szöveges oszlopos típushoz a Dimensions tábla használatával.
       - A riasztásokat az egyedi kombinációk és a riasztások hasznos adatai alapján külön kell eldobni.
    
        ![Összesítési paraméterek és felosztás kiválasztása](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. Az **előnézeti** diagram a lekérdezés-értékelések eredményeit jeleníti meg az idő múlásával. Megváltoztathatja a diagram időszakát, vagy kiválaszthat egy eltérő idősorozatot, amely az egyedi riasztások dimenziók szerinti felosztását eredményezte.

        ![Előnézeti diagram](media/alerts-log/preview-chart.png)

    1. Ezután az előnézeti érték alapján állítsa be a **riasztási logikát**; [ **Operátor**, **küszöbérték**](../platform/alerts-unified-log.md#threshold-and-operator)és [**gyakoriság**](../platform/alerts-unified-log.md#frequency).

        ![Küszöbértéket és riasztási logikát tartalmazó diagram előnézete](media/alerts-log/chart-and-alert-logic.png)

    1. Megadhatja [**a riasztások számának**](../platform/alerts-unified-log.md#number-of-violations-to-trigger-alert) megadását a **Speciális beállítások** szakaszban.
    
        ![Speciális beállítások](media/alerts-log/advanced-options.png)

1. A **műveletek** lapon válassza ki vagy hozza létre a szükséges [műveleti csoportokat](../platform/action-groups.md).

    ![Műveletek lap](media/alerts-log/actions-tab.png)

1. A **részletek** lapon adja meg a **riasztási szabály részleteit** és a **projekt részleteit**. Opcionálisan beállíthatja, hogy a **rendszer ne kezdjen el**, vagy a riasztási szabály elindítását követően [**elnémítani a műveleteket**](../platform/alerts-unified-log.md#state-and-resolving-alerts) .

    > [!NOTE]
    > A naplózási riasztási szabályok jelenleg állapot nélküliek, és minden alkalommal elindítanak egy műveletet, ha az Elnémítás nincs meghatározva.

    ![Részletek lap](media/alerts-log/details-tab.png)

1. A **címkék** lapon adja meg a szükséges címkéket a riasztási szabály erőforráson.

    ![Címkék lap](media/alerts-log/tags-tab.png)

1. A **felülvizsgálat + létrehozás** lapon egy ellenőrzés fog futni, és tájékoztatja az esetleges problémákról. Tekintse át és hagyja jóvá a szabály definícióját.
1. Ha az összes mező helyes, válassza a **Létrehozás** gombot, és fejezze be a riasztási szabály létrehozását. Minden riasztás a riasztások kezelése szolgáltatásban tekinthető meg.
 
    ![Áttekintés és Létrehozás lap](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Tekintse meg & a naplózási riasztások kezelése Azure Portal

1. A [portálon](https://portal.azure.com/)válassza ki a megfelelő erőforrást vagy **figyelő** szolgáltatást. Ezután válassza a **riasztások** lehetőséget a figyelő szakaszban.

1. A riasztások kezelése megjeleníti az összes kilőtt riasztást. [További információ a riasztások kezeléséről](alerts-managing-alert-instances.md).

    > [!NOTE]
    > A naplózási riasztási szabályok jelenleg [állapot nélküliek, és nem oldhatók](../platform/alerts-unified-log.md#state-and-resolving-alerts)fel.

1. A szabályok szerkesztéséhez kattintson a **riasztási szabályok kezelése** gombra a felső sávon:

    ![ riasztási szabályok kezelése](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>A naplózási riasztások kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> A PowerShell jelenleg nem támogatott az API-verzióban `2020-05-01-preview`

Az alábbi PowerShell-parancsmagok érhetők el a szabályok kezeléséhez az [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/)-val.

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell-parancsmag új naplózási riasztási szabály létrehozásához.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell-parancsmag egy meglévő naplózási riasztási szabály frissítéséhez.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely megadja a naplózási riasztás forrásának paramétereit. A [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely meghatározza a naplózási riasztások ütemezett paramétereit. A [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely a naplózási riasztás műveleti paramétereit határozza meg. A [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) és a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez a műveleti csoportok paramétereinek megadása a naplózási riasztásokhoz. A [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely meghatározza a riasztási feltétel paramétereit. A [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) parancsmag bemenetként használja.
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell-parancsmag az objektum létrehozásához vagy frissítéséhez, amely metrikai trigger feltételi paramétereit adja meg a [metrikai mérték típusú napló riasztásához](../platform/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value). A [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) parancsmag bemenetként használja.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell-parancsmag a meglévő naplózási riasztási szabályok listázásához vagy egy adott napló riasztási szabályához
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell-parancsmag a napló riasztási szabályának engedélyezéséhez vagy letiltásához
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-parancsmag egy meglévő naplózási riasztási szabály törléséhez

> [!NOTE]
> A ScheduledQueryRules PowerShell-parancsmagok csak az aktuális [ütemezett lekérdezési szabályok API](/rest/api/monitor/scheduledqueryrules/)-ban létrehozott szabályokat kezelhetik. Az örökölt [log Analytics riasztási API](../platform/api-alerts.md) használatával létrehozott naplózási riasztási szabályok csak a PowerShell használatával kezelhetők csak [az ütemezett lekérdezési szabályok API-ra való váltás](../alerts/alerts-log-api-switch.md)után.

Íme néhány példa a log riasztási szabály létrehozásához a PowerShell használatával:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

A log-riasztást [sablon és paraméterek](./alerts-log-create-templates.md) fájlok használatával is létrehozhatja a PowerShell használatával:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>A naplózási riasztások kezelése a parancssori felület használatával

> [!NOTE]
> Az Azure CLI-támogatás csak a scheduledQueryRules API és újabb verziókban érhető el `2020-05-01-preview` . A áteresztő API-verzió az alább leírtak szerint használhatja a Azure Resource Manager CLI-t a sablonokkal. Ha az örökölt [log Analytics riasztási API](../platform/api-alerts.md)-t használja, a CLI használatára kell váltania. [További információ a váltásról](./alerts-log-api-switch.md).

Az előző szakasz ismerteti, hogyan hozhatók létre, tekinthetők meg és kezelhetők a naplózási riasztási szabályok a Azure Portal használatával. Ez a szakasz azt írja le, hogyan végezheti el ugyanezt a platformfüggetlen [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával. Az Azure CLI használatának leggyorsabb módja a [Azure Cloud Shellon](../../cloud-shell/overview.md)keresztül. Ebben a cikkben a Cloud Shell fogjuk használni.

1. Lépjen a Azure Portalra, majd válassza a **Cloud Shell** lehetőséget.

1. A parancssorban a ``--help`` parancsokkal további információkat kaphat a paranccsal és annak használatáról. A következő parancs például megjeleníti a naplók létrehozásának, megtekintésének és kezeléséhez elérhető parancsok listáját:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. Létrehozhat egy olyan naplózási riasztási szabályt, amely figyeli a rendszeresemény-hibák számát:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. Az erőforráscsoporthoz tartozó összes naplózási riasztást a következő parancs használatával tekintheti meg:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. Egy adott napló riasztási szabályának részleteit a szabály nevével vagy erőforrás-azonosítójával tekintheti meg:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. A következő paranccsal tilthatja le a naplózási riasztási szabályt:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. A következő parancs használatával törölheti a napló riasztási szabályait:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Azure Resource Manager parancssori felületet is használhatja a [sablonok](./alerts-log-create-templates.md) fájljaival:

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

A létrehozás sikeressége érdekében a 201 értéket adja vissza. A frissítés sikerességéről a 200 értéket adja vissza.

## <a name="next-steps"></a>Következő lépések

* További információ a [naplózási riasztásokról](../platform/alerts-unified-log.md).
* Napló-riasztások létrehozása [Azure Resource Manager-sablonok](./alerts-log-create-templates.md)használatával.
* [A naplózási riasztások webhook-műveleteinek](./alerts-log-webhook.md)ismertetése.
* További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md).
