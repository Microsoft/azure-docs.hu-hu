---
title: Számítási feladat figyelése – Azure Portal
description: Monitor Synapse SQL a Azure Portal
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568267"
---
# <a name="monitor-workload---azure-portal"></a>Számítási feladat figyelése – Azure Portal

Ez a cikk bemutatja, hogyan használhatja a Azure Portal a számítási feladatok figyelése érdekében. Ez magában foglalja a naplók Azure Monitor a lekérdezések végrehajtásával és a számítási feladatok trendjeinek vizsgálatával a naplóelemzés segítségével [a Synapse SQL.](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, [a](https://azure.microsoft.com/free/) kezdés előtt hozzon létre egy ingyenes fiókot.
- SQL-készlet: Egy SQL-készlet naplóit gyűjtjük be. Ha még nincs kiépítve SQL-készlete, tekintse meg [az SQL-készlet létrehozása című útmutatót.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Lépjen a Log Analytics-munkaterületek tallózási paneljére, és hozzon létre egy munkaterületet

![Log Analytics-munkaterületek](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Képernyőkép a Log Analytics-munkaterületről, ahol kiválaszthatja a Hozzáadás lehetőséget.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Képernyőkép a Log Analytics-munkaterületről, ahol értékeket lehet megadni.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

A munkaterületekkel kapcsolatos további részletekért keresse fel a következő [dokumentációt.](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-resource-logs"></a>Erőforrásnaplók bekapcsolása

Konfigurálja a diagnosztikai beállításokat úgy, hogy naplókat bocsátanak ki az SQL-készletből. A naplók a leggyakrabban használt teljesítmény-hibaelhárítási DMV-knek megfelelő telemetriai nézetekből állnak. Jelenleg a következő nézetek támogatottak:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Erőforrásnaplók engedélyezése](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

A naplók az Azure Storage-ba, a Stream Analytics a Log Analyticsbe bocsáthatók ki. Ebben az oktatóanyagban válassza a Log Analytics lehetőséget.

![Naplók megadása](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Lekérdezések futtatása a Log Analyticsen

Lépjen a Log Analytics-munkaterületre, ahol a következőket teheti:

- Naplók elemzése naplólekérdezésekkel és lekérdezések mentése újbóli felhasználáshoz
- Lekérdezések mentése újrafelhasználáshoz
- Naplóriasztások létrehozása
- Lekérdezési eredmények irányítópulton való kitűzése

A naplólekérdezések képességeivel kapcsolatos részletekért látogasson el a következő [dokumentációba.](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)

![Log Analytics-munkaterület szerkesztője](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics-munkaterület lekérdezései](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Naplólekérdezések mintája

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

Most, hogy beállította és konfigurálta [](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az Azure Monitor naplóit, szabja testre az Azure-irányítópultokat, hogy megosztja őket a csapatával.