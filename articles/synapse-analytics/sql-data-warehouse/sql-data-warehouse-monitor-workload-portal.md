---
title: Munkaterhelés figyelése – Azure Portal
description: A szinapszis SQL figyelése a Azure Portal használatával
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 327174974affb3b2511eac60755aa1bf047b3b5e
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133469"
---
# <a name="monitor-workload---azure-portal"></a>Munkaterhelés figyelése – Azure Portal

Ez a cikk azt ismerteti, hogyan használható a Azure Portal a számítási feladatok figyelésére. Ebbe beletartozik Azure Monitor naplók beállítása a lekérdezés-végrehajtás és a számítási feladatok trendjeinek vizsgálatára a [SZINAPSZIS SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)-hez készült log Analytics használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
- SQL-készlet: a rendszer egy SQL-készlet naplóit gyűjti. Ha nem rendelkezik kiépített SQL-készlettel, tekintse meg az [SQL-készlet létrehozása](load-data-from-azure-blob-storage-using-polybase.md)című témakör utasításait.

## <a name="create-a-log-analytics-workspace"></a>A Log Analytics-munkaterület létrehozása

Navigáljon Log Analytics munkaterületek Tallózás paneljére, és hozzon létre egy munkaterületet

![Log Analytics-munkaterületek](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Elemzési munkaterület hozzáadása](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Elemzési munkaterület hozzáadása](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

A munkaterületekkel kapcsolatos további információkért tekintse meg az alábbi [dokumentációt](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Erőforrás-naplók bekapcsolása

A diagnosztikai beállítások konfigurálása az SQL-készletből származó naplók kibocsátása céljából. A naplók a leggyakrabban használt teljesítmény-hibaelhárítási DMV egyenértékű telemetria-nézetekből állnak. Jelenleg a következő nézetek támogatottak:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Erőforrás-naplók engedélyezése](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

A naplók az Azure Storage, Stream Analytics vagy Log Analytics számára is kiállíthatók. Ebben az oktatóanyagban válassza a Log Analytics lehetőséget.

![Naplók meghatározása](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Lekérdezések futtatása Log Analytics

Navigáljon a Log Analytics munkaterületre, ahol a következőket teheti:

- Naplók elemzése a naplófájlok használatával és a lekérdezések ismételt újbóli lekérdezése
- Lekérdezések mentése ismételt használatra
- Naplóriasztások létrehozása
- Lekérdezési eredmények rögzítése egy irányítópulton

A naplózott lekérdezések képességeiről a következő [dokumentációban](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)olvashat bővebben.

![Log Analytics munkaterület-szerkesztő](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics munkaterület-lekérdezések](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Példák a naplók lekérdezésére

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>További lépések

Most, hogy beállította és konfigurálta az Azure monitor-naplókat, [testreszabhatja az Azure-irányítópultokat](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a csapaton belüli megosztáshoz.
