---
title: Lassú lekérdezési naplók – Azure Database for MySQL
description: A Azure Database for MySQLban elérhető lassú lekérdezési naplókat, valamint a különböző naplózási szintek engedélyezéséhez elérhető paramétereket ismerteti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: e3342b38f782b718cefd63295ef4d4d26b525058
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259047"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Lassú lekérdezési naplók az Azure Database for MySQL-ben
Azure Database for MySQL a lassú lekérdezési napló elérhető a felhasználók számára. A tranzakciónaplóhoz való hozzáférés nem támogatott. A lassú lekérdezési napló használatával azonosíthatók a hibaelhárítási teljesítménybeli szűk keresztmetszetek.

További információ a MySQL lassú lekérdezési naplóról: a MySQL-hivatkozás manuális [lassú lekérdezési napló szakasza](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

Ha a [lekérdezési tároló](concepts-query-store.md) engedélyezve van a kiszolgálón, láthatja a lekérdezéseket, például a " `CALL mysql.az_procedure_collect_wait_stats (900, 30);` " a lassú lekérdezések naplójában. Ez a viselkedés várható, mivel a lekérdezési tároló szolgáltatás statisztikai adatokat gyűjt a lekérdezésekről. 

## <a name="configure-slow-query-logging"></a>Lassú lekérdezések naplózásának konfigurálása 
Alapértelmezés szerint a lassú lekérdezési napló le van tiltva. Az engedélyezéséhez állítsa be értékre a következőre: `slow_query_log` . Ez a Azure Portal vagy az Azure CLI használatával engedélyezhető. 

Az egyéb paraméterek a következők:

- **long_query_time**: Ha a lekérdezés a lekérdezés naplózása long_query_time (másodpercben) hosszabb időt vesz igénybe. Az alapértelmezett érték 10 másodperc.
- **log_slow_admin_statements**: Ha a on olyan felügyeleti utasítások is szerepelnek, mint például a ALTER_TABLE és a ANALYZE_TABLE a slow_query_logba írt utasításokban.
- **log_queries_not_using_indexes**: meghatározza, hogy az indexeket nem használó lekérdezések naplózva vannak-e a slow_query_log
- **log_throttle_queries_not_using_indexes**: Ez a paraméter korlátozza a lassú lekérdezési naplóba írható nem indexelt lekérdezések számát. Ez a paraméter akkor lép életbe, amikor a log_queries_not_using_indexes be van állítva.
- **log_output**: Ha a "fájl", lehetővé teszi a lassú lekérdezési napló írását a helyi kiszolgáló tárterületére és a diagnosztikai naplók Azure Monitorére. Ha „Nincs”, a lassú lekérdezési napló csak az Azure Monitor Diagnostics Logsban lesz rögzítve. 

> [!IMPORTANT]
> Ha a táblák nincsenek indexelve, a (z) `log_queries_not_using_indexes` és a `log_throttle_queries_not_using_indexes` Paraméterek beállítása hatással lehet a MySQL-teljesítményre, mivel a nem indexelt táblákon futó összes lekérdezés a lassú lekérdezési naplóba kerül.<br><br>
> Ha hosszabb ideig szeretné naplózni a lassú lekérdezéseket, javasolt a "None" értékre állítani `log_output` . Ha a "file" értékre van állítva, a rendszer a helyi kiszolgáló tárolóba írja a naplókat, és hatással lehet a MySQL teljesítményére. 

A lassú lekérdezési napló paramétereinek teljes leírását a MySQL [lassú lekérdezési napló dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) találja.

## <a name="access-slow-query-logs"></a>Lassú lekérdezési naplók elérése
Két lehetőség van a lassú lekérdezési naplók elérésére Azure Database for MySQL: helyi kiszolgáló tárolóban vagy Azure Monitor diagnosztikai naplókban. Ez a paraméter használatával állítható be `log_output` .

A helyi kiszolgáló tárolásához a Azure Portal vagy az Azure CLI használatával lekérheti és letöltheti a lassú lekérdezési naplókat. A Azure Portalban navigáljon a kiszolgálóhoz a Azure Portal. A **figyelés** fejléc alatt válassza ki a **kiszolgálói naplók** lapot. Az Azure CLI-vel kapcsolatos további információkért lásd: [lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával](howto-configure-server-logs-in-cli.md). 

Azure Monitor diagnosztikai naplók lehetővé teszik a lassú lekérdezési naplók lekérését Azure Monitor naplókba (Log Analytics), az Azure Storage-ba vagy a Event Hubsba. További információért lásd [alább](concepts-server-logs.md#diagnostic-logs) .

## <a name="local-server-storage-log-retention"></a>Helyi kiszolgáló tárolási naplójának megőrzése
A kiszolgáló helyi tárolójába való bejelentkezéskor a naplók akár hét napig is elérhetők a létrehozásuk után. Ha a rendelkezésre álló naplók teljes mérete meghaladja a 7 GB-ot, a rendszer törli a legrégebbi fájlokat, amíg a szabad terület elérhetővé válik. A kiszolgálói naplók 7 GB-os tárolási korlátja ingyenesen elérhető, és nem terjeszthető ki. 

A naplók 24 óránként vagy 7 GB-onként vannak elforgatva, attól függően, hogy melyik érkezik először.

> [!Note]
> A fenti napló megőrzése nem vonatkozik a Azure Monitor diagnosztikai naplók használatával vezetékes naplókra. Megváltoztathatja a beérkező adatnyelők megőrzési időtartamát (pl. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for MySQL integrálva van Azure Monitor diagnosztikai naplókba. Ha engedélyezte a lassú lekérdezési naplókat a MySQL-kiszolgálón, kiválaszthatja, hogy a naplókat, Event Hubsokat vagy az Azure Storage-t Azure Monitor. A diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért tekintse meg a [diagnosztikai naplók dokumentációjának](../azure-monitor/essentials/platform-logs-overview.md)útmutató szakaszát.

Az alábbi táblázat az egyes naplókat ismerteti. A kimeneti módszertől függően a befoglalt mezők és a megjelenő sorrend eltérő lehet.

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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | AZONOSÍTÓ beszúrása |
| `sql_text_s` | Teljes lekérdezés |
| `server_id_s` | A kiszolgáló azonosítója |
| `thread_id_s` | Szál azonosítója |
| `\_ResourceId` | Erőforrás URI-ja |

> [!Note]
> A esetében a rendszer `sql_text` csonkolja a naplót, ha az meghaladja a 2048 karaktert.

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
- [Lassú lekérdezési naplók konfigurálása a Azure Portal](howto-configure-server-logs-in-portal.md)
- [Lassú lekérdezések naplóinak konfigurálása az Azure CLI-ből](howto-configure-server-logs-in-cli.md)
