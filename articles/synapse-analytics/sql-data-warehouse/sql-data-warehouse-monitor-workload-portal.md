---
title: Munkaterhelés figyelése – Azure Portal
description: A szinapszis SQL figyelése a Azure Portal használatával
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: 3fb81ffc24ed6073e5398b14b7f490a0f63df84f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585587"
---
# <a name="monitor-workload---azure-portal"></a>Munkaterhelés figyelése – Azure Portal

Ez a cikk azt ismerteti, hogyan használható a Azure Portal a számítási feladatok figyelésére. Ebbe beletartozik Azure Monitor naplók beállítása a lekérdezés-végrehajtás és a számítási feladatok trendjeinek vizsgálatára a [SZINAPSZIS SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)-hez készült log Analytics használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
- SQL-készlet: a rendszer egy SQL-készlet naplóit gyűjti. Ha nem rendelkezik kiépített SQL-készlettel, tekintse meg az [SQL-készlet létrehozása](./load-data-from-azure-blob-storage-using-copy.md)című témakör utasításait.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Navigáljon Log Analytics munkaterületek Tallózás paneljére, és hozzon létre egy munkaterületet

![Log Analytics-munkaterületek](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Képernyőfelvétel: a Log Analytics munkaterületek, ahol a Hozzáadás lehetőséget választhatja.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![A képernyőképen a Log Analytics munkaterület látható, ahol értékeket adhat meg.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

A munkaterületekkel kapcsolatos további információkért tekintse meg az alábbi [dokumentációt](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Erőforrás-naplók bekapcsolása

A diagnosztikai beállítások konfigurálása az SQL-készletből származó naplók kibocsátása céljából. A naplók a leggyakrabban használt teljesítmény-hibaelhárítási DMV egyenértékű telemetria-nézetekből állnak. Jelenleg a következő nézetek támogatottak:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Erőforrás-naplók engedélyezése](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

A naplók az Azure Storage, Stream Analytics vagy Log Analytics számára is kiállíthatók. Ebben az oktatóanyagban válassza a Log Analytics lehetőséget.

![Naplók meghatározása](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Lekérdezések futtatása Log Analytics

Navigáljon a Log Analytics munkaterületre, ahol a következőket teheti:

- Naplók elemzése a naplófájlok használatával és a lekérdezések ismételt újbóli lekérdezése
- Lekérdezések mentése ismételt használatra
- Naplóriasztások létrehozása
- Lekérdezési eredmények rögzítése egy irányítópulton

A naplózott lekérdezések képességeiről a következő [dokumentációban](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)olvashat bővebben.

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

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította és konfigurálta az Azure monitor-naplókat, [testreszabhatja az Azure-irányítópultokat](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a csapaton belüli megosztáshoz.