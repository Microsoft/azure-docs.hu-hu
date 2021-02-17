---
title: Lassú lekérdezési naplók – Azure Database for MySQL – rugalmas kiszolgáló
description: A Azure Database for MySQL rugalmas kiszolgálón elérhető lassú lekérdezési naplókat ismerteti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579195"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Lassú lekérdezési naplók Azure Database for MySQL rugalmas kiszolgálóban (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Azure Database for MySQL rugalmas kiszolgáló esetében a lassú lekérdezési napló elérhető a felhasználók számára a konfiguráláshoz és a hozzáféréshez. A lassú lekérdezési naplók alapértelmezés szerint le vannak tiltva, és a hibaelhárítás során a teljesítménnyel kapcsolatos szűk keresztmetszetek azonosítására is lehetőségük van.

A MySQL lassú lekérdezési naplóval kapcsolatos további információkért tekintse meg a MySQL motor dokumentációjának [lassú lekérdezési napló szakaszát](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) .

## <a name="configure-slow-query-logging"></a>Lassú lekérdezések naplózásának konfigurálása 
Alapértelmezés szerint a lassú lekérdezési napló le van tiltva. A naplók engedélyezéséhez állítsa be a `slow_query_log` kiszolgáló paraméterét *a* következőre:. Ezt a Azure Portal vagy az Azure CLI használatával lehet konfigurálni <!-- add link to server parameter-->. 

Egyéb paraméterek a lassú lekérdezés naplózási viselkedésének vezérléséhez:

- **long_query_time**: a lekérdezés naplózása, ha a művelet `long_query_time` befejezéséhez (másodpercben) hosszabb időt vesz igénybe. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements**: meghatározza, hogy a felügyeleti utasítások (pl. `ALTER_TABLE`, `ANALYZE_TABLE` ) naplózásra kerül.
- **log_queries_not_using_indexes**: meghatározza, hogy az indexeket nem használó lekérdezések naplózva vannak-e.
- **log_throttle_queries_not_using_indexes**: korlátozza a lassú lekérdezési naplóba írható nem indexelt lekérdezések számát. Ez a paraméter akkor lép életbe, ha `log_queries_not_using_indexes` be van állítva 

> [!IMPORTANT]
> Ha a táblák nincsenek indexelve, a (z) `log_queries_not_using_indexes` és a `log_throttle_queries_not_using_indexes` Paraméterek beállítása hatással lehet a MySQL-teljesítményre, mivel a nem indexelt táblákon futó összes lekérdezés a lassú lekérdezési naplóba kerül. 

A lassú lekérdezési napló paramétereinek teljes leírását a MySQL [lassú lekérdezési napló dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) találja.

## <a name="access-slow-query-logs"></a>Lassú lekérdezési naplók elérése

A lassú lekérdezési naplók integrálva vannak Azure Monitor diagnosztikai beállításokkal. Miután engedélyezte a lassú lekérdezési naplókat a MySQL rugalmas kiszolgálón, kibocsáthatja őket Azure Monitor naplókba, Event Hubsba vagy az Azure Storage szolgáltatásba. A diagnosztikai beállításokkal kapcsolatos további tudnivalókért tekintse meg a [diagnosztikai naplók dokumentációját](../../azure-monitor/essentials/platform-logs-overview.md). Ha többet szeretne megtudni arról, hogyan engedélyezheti a diagnosztikai beállításokat a Azure Portalban, tekintse meg a [lassú lekérdezési napló-portált ismertető cikket](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

A következő táblázat a lassú lekérdezési napló kimenetét ismerteti. A kimeneti módszertől függően a befoglalt mezők és a megjelenő sorrend eltérő lehet.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | A kiszolgáló neve |
| `start_time_t` UTC | A lekérdezés elkezdésének ideje |
| `query_time_s` | A lekérdezés végrehajtásának teljes időtartama (másodpercben) |
| `lock_time_s` | A lekérdezés zárolásának teljes ideje másodpercben |
| `user_host_s` | Felhasználónév |
| `rows_sent_s` | Elérkezett sorok száma |
| `rows_examined_s` | Megvizsgált sorok száma |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | AZONOSÍTÓ beszúrása |
| `sql_text_s` | Teljes lekérdezés |
| `server_id_s` | A kiszolgáló azonosítója |
| `thread_id_s` | Szál azonosítója |
| `\_ResourceId` | Erőforrás URI-ja |

> [!Note]
> A esetében a rendszer `sql_text_s` csonkolja a naplót, ha az meghaladja a 2048 karaktert.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Naplók elemzése Azure Monitor naplókban

Ha a lassú lekérdezési naplók a diagnosztikai naplókon keresztül Azure Monitor naplókba, a lassú lekérdezések további elemzését is elvégezheti. Az alábbiakban néhány példa olvasható, amely segítséget nyújt az első lépésekhez. Győződjön meg arról, hogy az alábbit frissíti a kiszolgáló nevével.

- 10 másodpercnél hosszabb lekérdezések egy adott kiszolgálón

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Egy adott kiszolgálón az 5 leggyakoribb leghosszabb lekérdezés listázása

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Lassú lekérdezések összefoglalása egy adott kiszolgálón a minimális, a maximális, az átlag és a szórásos lekérdezési idő alapján

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Egy adott kiszolgáló lassú lekérdezési eloszlásának diagramja

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 10 másodpercnél hosszabb lekérdezések megjelenítése az összes olyan MySQL-kiszolgálón, amelyen engedélyezve vannak a diagnosztikai naplók

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Következő lépések
- További információ a [naplókról](concepts-audit-logs.md)
- Lassú lekérdezési naplók konfigurálása a [Azure Portal](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->