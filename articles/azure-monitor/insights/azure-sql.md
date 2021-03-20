---
title: Azure SQL Analytics megoldás a Azure Monitorban | Microsoft Docs
description: Azure SQL Analytics megoldás segítségével kezelheti Azure SQL-adatbázisait
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 54ef88e65925ba9c7e9fe2e44ef0c76fbc9ceb04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717485"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Database figyelése Azure SQL Analytics használatával (előzetes verzió)

![Azure SQL Analytics szimbólum](./media/azure-sql/azure-sql-symbol.png)

A Azure SQL Analytics egy fejlett felhőalapú figyelési megoldás, amely az összes Azure SQL Database-adatbázis teljesítményét és egyetlen nézetben több előfizetést is figyel. A Azure SQL Analytics a teljesítménnyel kapcsolatos hibaelhárításhoz beépített intelligenciával rendelkező fő teljesítménymutatókat gyűjti és jeleníti meg.

Az összegyűjtött metrikák használatával egyéni figyelési szabályokat és riasztásokat hozhat létre. Azure SQL Analytics segítségével azonosíthatja a problémákat az alkalmazás-verem minden rétegében. Az Azure diagnosztikai metrikákat és Azure Monitor nézeteket használ, hogy az összes Azure SQL-adatbázis adatait egyetlen Log Analytics munkaterületen lehessen bemutatni. Azure Monitor a strukturált és strukturálatlan adatok összegyűjtését, összekapcsolását és megjelenítését teszi lehetővé.

A Azure SQL Analytics megoldás használatáról és a tipikus használati forgatókönyvekről a beágyazott videóban talál további információt:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Összekapcsolt források

A Azure SQL Analytics egy Felhőbeli figyelési megoldás, amely támogatja az összes Azure SQL-adatbázis diagnosztikai telemetria folyamatos átvitelét. Mivel Azure SQL Analytics nem használ ügynököket Azure Monitorhoz való kapcsolódásra, nem támogatja a helyszíni vagy virtuális gépeken üzemeltetett SQL Server figyelését.

| Összekapcsolt forrás | Támogatott | Description |
| --- | --- | --- |
| [Diagnosztikai beállítások](../essentials/diagnostic-settings.md) | **Igen** | Az Azure metrika és a naplózási adatok közvetlenül az Azure-ban érkeznek Azure Monitor naplókra. |
| [Azure Storage-fiók](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Azure Monitor nem olvassa be a Storage-fiók adatait. |
| [Windows-ügynökök](../agents/agent-windows.md) | No | Azure SQL Analytics nem használja a közvetlen Windows-ügynököket. |
| [Linux-ügynökök](../vm/quick-collect-linux-computer.md) | No | Azure SQL Analytics nem használja a közvetlen Linux-ügynököket. |
| [System Center Operations Manage felügyeleti csoport](../agents/om-agents.md) | No | A Azure SQL Analytics nem használja közvetlen kapcsolódást a Operations Manager ügynöktől a Azure Monitor. |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics beállítások

Az alábbi táblázat a Azure SQL Analytics irányítópult két verziójának támogatott lehetőségeit ismerteti, amelyek közül az egyiket Azure SQL Database, a másikat pedig az Azure SQL felügyelt példány-adatbázisaihoz.

| Azure SQL Analytics lehetőség | Description | SQL Database támogatás | Az SQL Managed Instance támogatása |
| --- | ------- | ----- | ----- |
| Erőforrás típus szerint | Az összes figyelt erőforrást megszámoló perspektíva. | Igen | Yes |
| Insights | Hierarchikus részletezést biztosít a Intelligent Insightsba a teljesítményig. | Igen | Yes |
| Hibák | Hierarchikus részletezést biztosít az adatbázisokon történt SQL-hibákhoz. | Igen | Yes |
| Időtúllépések | Hierarchikus részletezést biztosít az adatbázisokon történt SQL-időtúllépésekhez. | Igen | Nem |
| Tiltások | Hierarchikus részletezést biztosít az adatbázisokon történt SQL-blokkokhoz. | Igen | Nem |
| Adatbázis-várakozások | Hierarchikus részletezést biztosít az SQL WAIT statisztikái számára az adatbázis szintjén. A várakozási idő összegzését és a várakozási idő várakozási idejét foglalja magában. |Igen | Nem |
| Lekérdezés időtartama | Hierarchikus részletezést biztosít a lekérdezés végrehajtási statisztikái, például a lekérdezési időtartam, a CPU-használat, az adatio-használat, a log IO használata során. | Igen | Yes |
| Lekérdezési várakozások | Hierarchikus részletezést biztosít a lekérdezési várakozási statisztikákra a várakozási kategóriánként. | Igen | Yes |

## <a name="configuration"></a>Konfiguráció

Használja a [Solutions Gallery Azure monitor-megoldások hozzáadása az](./solutions.md) Azure SQL Analytics (előzetes verzió) a log Analytics munkaterülethez való hozzáadásához használt eljárást.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Azure SQL Database konfigurálása a stream Diagnostics telemetria

Miután létrehozta Azure SQL Analytics megoldást a munkaterületen, **konfigurálnia** kell az összes figyelni kívánt erőforrást, hogy a diagnosztikai telemetria továbbítsa a Azure SQL Analytics. Kövesse az oldalon található részletes utasításokat:

- Engedélyezze Azure Diagnostics az adatbázis számára, hogy a [diagnosztikai telemetria Azure SQL Analytics](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

A fenti oldalon megtudhatja, hogyan engedélyezheti a több Azure-előfizetés figyelését egyetlen Azure SQL Analytics munkaterületről egyetlen üvegtáblaként.

## <a name="using-azure-sql-analytics"></a>Azure SQL Analytics használata

Azure SQL Analytics a munkaterülethez való hozzáadásakor a rendszer hozzáadja a Azure SQL Analytics csempét a munkaterülethez, és megjelenik az Áttekintés területen. A csempe tartalmának betöltéséhez válassza az összefoglalás megtekintése hivatkozást.

![Azure SQL Analytics összefoglaló csempe](./media/azure-sql/azure-sql-sol-tile-01.png)

A betöltést követően a csempén látható az adatbázisok és a rugalmas készletek száma a SQL Database, valamint a példányok és példányok adatbázisaiban az SQL felügyelt példányában, amelyből a Azure SQL Analytics diagnosztikai telemetria kap.

![Azure SQL Analytics csempe](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics két különálló nézetet biztosít – egyet a figyelési SQL Databasehoz, valamint a felügyelt SQL-példány figyelésének másik nézetét.

A SQL Database Azure SQL Analytics monitorozási irányítópultjának megtekintéséhez kattintson a csempe felső részén. A felügyelt SQL-példányok Azure SQL Analytics figyelési irányítópultjának megtekintéséhez kattintson a csempe alsó részére.

### <a name="viewing-azure-sql-analytics-data"></a>AdatAzure SQL Analyticsek megtekintése

Az irányítópult a különböző perspektívák által figyelt adatbázisok áttekintését tartalmazza. A különböző perspektívák működéséhez engedélyeznie kell a megfelelő metrikákat vagy naplókat az SQL-erőforrásokon a Log Analytics munkaterületre való továbbításhoz.

Ha egyes mérőszámok vagy naplók nem áramlanak be Azure Monitorba, a Azure SQL Analytics csempéi nem lesznek kitöltve a figyelési információkkal.

### <a name="sql-database-view"></a>SQL Database nézet

Miután kiválasztotta az adatbázis Azure SQL Analytics csempét, megjelenik a figyelési irányítópult.

![A figyelési irányítópultot bemutató képernyőkép.](./media/azure-sql/azure-sql-sol-overview.png)

A csempék bármelyikének kiválasztásával megnyithatja a részletezési jelentést az adott perspektívában. A perspektíva kiválasztását követően megnyílik a részletezési jelentés.

![Képernyőkép, amely megjeleníti a részletezési jelentést az adott perspektívában.](./media/azure-sql/azure-sql-sol-metrics.png)

Az ebben a nézetben szereplő összes perspektíva összefoglalókat biztosít az előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázis szintjén. Emellett minden perspektívában látható a jelentésre vonatkozó perspektíva a jobb oldalon. Ha kijelöli az előfizetést, a kiszolgálót, a készletet vagy az adatbázist a listából, folytatja a részletezést.

### <a name="sql-managed-instance-view"></a>SQL felügyelt példány nézet

Miután kiválasztotta az adatbázisok Azure SQL Analytics csempét, megjelenik a figyelési irányítópult.

![Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview-mi.png)

A csempék bármelyikének kiválasztásával megnyithatja a részletezési jelentést az adott perspektívában. A perspektíva kiválasztását követően megnyílik a részletezési jelentés.

Az SQL felügyelt példány nézetének kiválasztásával megtekintheti a felügyelt példányon végrehajtott lekérdezésekben a példány kihasználtsága, a példány adatbázisai és a telemetria részleteit.

![Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights jelentés

Azure SQL Database [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) segítségével megtudhatja, mi történik az összes Azure SQL-adatbázis teljesítményével. Az összegyűjtött Intelligent Insights a bepillantások perspektívájában megjeleníthetők és elérhetők.

![Azure SQL Analyticsi adatfelismerés](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Rugalmas készletek és adatbázis-jelentések

A rugalmas készletek és adatbázisok saját jelentésekkel rendelkeznek, amelyek az adott időszakban az adott erőforráshoz összegyűjtött összes adatokat megjelenítik.

![Adatbázis Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL rugalmas készlet](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Jelentések lekérdezése

A lekérdezés időtartama és a lekérdezési várakozási idő alapján a lekérdezési jelentésen keresztül összekapcsolhatók a lekérdezések teljesítménye. Ez a jelentés összehasonlítja a lekérdezési teljesítményt a különböző adatbázisok között, és megkönnyíti a kiválasztott lekérdezési és lassú összehasonlítást végző adatbázisok azonosítását.

![Lekérdezések Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Engedélyek

A Azure SQL Analytics használatához a felhasználóknak minimális jogosultságot kell biztosítaniuk az olvasó szerepkörhöz az Azure-ban. Ez a szerepkör azonban nem teszi lehetővé a felhasználók számára a lekérdezési szöveg megtekintését, vagy Automatikus hangolási műveletek elvégzését. Az Azure-ban több, a tulajdonos, a közreműködő, az SQL-adatbázis közreműködője vagy a SQL Server közreműködő általi használatának lehetővé tételét engedélyező, az Azure-ban elérhetővé teszi az Azure SQL Analytics. Érdemes lehet egyéni szerepkört létrehozni a portálon olyan konkrét engedélyekkel, amelyek csak a Azure SQL Analytics használatához szükségesek, és nem férnek hozzá más erőforrások kezeléséhez.

### <a name="creating-a-custom-role-in-portal"></a>Egyéni szerepkör létrehozása a portálon

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Annak felismerése, hogy egyes szervezetek szigorú engedélyekkel rendelkeznek az Azure-ban, keresse meg a következő PowerShell-parancsfájlt, amely lehetővé teszi az "SQL Analytics monitoring Operator" nevű egyéni szerepkör létrehozását Azure Portal a Azure SQL Analytics teljes mértékben való használatához szükséges minimális olvasási és írási engedéllyel.

Cserélje le a "{SubscriptionId}" kifejezést az alábbi parancsfájlban az Azure-előfizetési AZONOSÍTÓra, és hajtsa végre a bejelentkezett parancsfájlt tulajdonosi vagy közreműködői szerepkörként az Azure-ban.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Az új szerepkör létrehozása után rendelje hozzá ezt a szerepkört minden olyan felhasználóhoz, akinek egyéni engedélyeket kell adnia a Azure SQL Analytics használatához.

## <a name="analyze-data-and-create-alerts"></a>Az adatelemzés és a riasztások létrehozása

Az adatelemzés Azure SQL Analytics az egyéni lekérdezés és jelentéskészítés [log Analytics nyelvén](../logs/get-started-queries.md) alapul. Az adatbázis-erőforrásból összegyűjtött rendelkezésre álló adatok leírását az egyéni lekérdezésekhez a [mérőszámok és naplók elérhetővé](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available)tételében találja.

A Azure SQL Analytics automatikus riasztásai olyan Log Analytics-lekérdezés írásán alapulnak, amely egy adott feltétel teljesülése esetén riasztást indít el. Az alábbi példákban talál néhány példát Log Analytics lekérdezésekre, amelyeken a riasztás beállítható Azure SQL Analyticsban.

### <a name="creating-alerts-for-azure-sql-database"></a>Riasztások létrehozása Azure SQL Databasehoz

Egyszerűen létrehozhat Azure SQL Database erőforrásokból érkező adatokkal kapcsolatos [riasztásokat](../alerts/alerts-metric.md) . Íme néhány hasznos, a log-riasztással használható [naplózási lekérdezés](../logs/log-query-overview.md) :

#### <a name="high-cpu"></a>Magas CPU-használat

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok az alapszintű mérőszámokat a Azure SQL Analyticsra továbbítsák.
> - Ehelyett cserélje le a MetricName értéket cpu_percent és dtu_consumption_percent a magas DTU eredmények beszerzéséhez.

#### <a name="high-cpu-on-elastic-pools"></a>Magas CPU rugalmas készleteken

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok az alapszintű mérőszámokat a Azure SQL Analyticsra továbbítsák.
> - Ehelyett cserélje le a MetricName értéket cpu_percent és dtu_consumption_percent a magas DTU eredmények beszerzéséhez.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Az elmúlt 1 HR-ben átlagosan 95%-nál nagyobb tárterület

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok az alapszintű mérőszámokat a Azure SQL Analyticsra továbbítsák.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel egyes adatbázisokon létezik. A kimenet azoknak az adatbázis-erőforrásoknak a listája, amelyek a megadott time_range storage_threshold felett vannak.
> - A kimenet azoknak az adatbázis-erőforrásoknak a listája, amelyek a megadott time_range storage_threshold felett vannak.

#### <a name="alert-on-intelligent-insights"></a>Intelligens adatfelismerések riasztása

> [!IMPORTANT]
> Ha egy adatbázis jól működik, és nincs Intelligent Insights generálva, a lekérdezés hibaüzenetet kap: nem sikerült feloldani a (z) "rootCauseAnalysis_s" nevű skaláris kifejezést. Ez a viselkedés minden olyan esetben várható, amikor nem létezik intelligens elemzés az adatbázishoz.

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok stream SQLInsights diagnosztikai naplója Azure SQL Analytics.
> - Ehhez a lekérdezéshez a riasztási szabályt úgy kell beállítani, hogy az ugyanazzal a gyakorisággal fusson, mint a alert_run_interval az eredmények elkerülése érdekében. A szabályt úgy kell beállítani, hogy kikapcsolja a riasztást, ha létezik eredmény (> 0 eredmény) a lekérdezésből.
> - A alert_run_interval testreszabásával megadhatja az időtartományt, amely alapján ellenőrizhető, hogy a feltétel bekövetkezett-e olyan adatbázisokon, amelyek a SQLInsights-naplónak a Azure SQL Analytics.
> - A insights_string testre szabásával rögzítheti az elemzések kiváltó okának elemzési szövegének eredményét. Ez az Azure SQL Analytics felhasználói felületén megjelenő szöveg, amelyet a meglévő adatokból használhat. Azt is megteheti, hogy az alábbi lekérdezéssel megtekinti az előfizetésen generált összes információ szövegét. A lekérdezés kimenetének használatával begyűjtheti a riasztások beállításához szükséges különböző karakterláncokat.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Riasztások létrehozása a felügyelt SQL-példányhoz

#### <a name="storage-is-above-90"></a>A tárterület 90%-nál nagyobb

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt felügyelt példányon engedélyezve legyen a ResourceUsageStats-napló folyamatos átvitele Azure SQL Analytics.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel létezik a felügyelt példányon. A kimenet a felügyelt példány tárolási százalékos aránya.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>A CPU átlagos fogyasztása 95% fölött van az elmúlt 1 HR-ben

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt felügyelt példányon engedélyezve legyen a ResourceUsageStats-napló folyamatos átvitele Azure SQL Analytics.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel létezik a felügyelt példányon. A kimenet a felügyelt példányon megadott időszakra vonatkozó átlagos CPU-kihasználtság százalékos aránya.

### <a name="pricing"></a>Díjszabás

Míg a Azure SQL Analytics ingyenesen használható, a diagnosztika telemetria az egyes hónapokban lefoglalt ingyenes adatfeldolgozási egységeken felüli felhasználást lásd: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/monitor). Az adatfeldolgozás ingyenes egysége lehetővé teszi, hogy minden hónapban több adatbázis ingyenes figyelése legyen elérhető. Nagyobb mennyiségű aktív adatbázis több adatot és üresjárati adatbázist is felhasznál. A Azure SQL Analytics navigációs menüjében a OMS munkaterület elem kiválasztásával, majd a használat és a becsült költségek lehetőség kiválasztásával egyszerűen figyelheti az adatok betöltésének felhasználását Azure SQL Analytics.

## <a name="next-steps"></a>Következő lépések

- A Azure Monitorban található [naplók](../logs/log-query-overview.md) használatával részletes Azure SQL-információk jeleníthetők meg.
- [Saját irányítópultokat hozhat létre](../visualize/tutorial-logs-dashboards.md) , amelyek az Azure SQL-adatait jelenítik meg.
- [Riasztások létrehozása](../alerts/alerts-overview.md) , ha adott Azure SQL-események történnek.

