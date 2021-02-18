---
title: Naplók – Azure Database for MySQL – rugalmas kiszolgáló
description: Ismerteti Azure Database for MySQL rugalmas kiszolgálón elérhető naplókat.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d38a0ea7307584c25ddec9389feaef7518bcf925
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591762"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Az adatbázis-tevékenységek nyomon követése Azure Database for MySQL rugalmas kiszolgáló naplófájljaival

> [!IMPORTANT] 
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

Azure Database for MySQL rugalmas kiszolgáló lehetővé teszi a felhasználók számára, hogy konfigurálja a naplókat. A naplók segítségével nyomon követhető az adatbázis-szintű tevékenységek, például a kapcsolódási, a felügyeleti, a DDL-és a DML-események. Ezek a naplótípusok általában a megfelelőség biztosítása érdekében használatosak.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

Alapértelmezés szerint a naplók le vannak tiltva. Az engedélyezéshez állítsa be a `audit_log_enabled` kiszolgálói paramétert *a* következőre:. Ezt a Azure Portal vagy az Azure CLI használatával lehet konfigurálni <!-- add link to server parameter-->. 

A naplózási naplózási viselkedés vezérléséhez beállítható egyéb paraméterek a következők:

- `audit_log_events`: a naplózandó események szabályozása. Tekintse meg az alábbi táblázatot az egyes naplózási eseményekhez.
- `audit_log_include_users`: A MySQL-felhasználók bekerülnek a naplózásba. A paraméter alapértelmezett értéke üres, amely tartalmazza a naplózáshoz szükséges összes felhasználót. Ennek magasabb prioritása van `audit_log_exclude_users` . A paraméter maximális hossza 512 karakter.
- `audit_log_exclude_users`: A rendszer kizárja a MySQL-felhasználókat a naplózásból. A paraméter maximális hossza 512 karakter.

> [!NOTE]
> `audit_log_include_users` magasabb prioritással rendelkezik `audit_log_exclude_users` . Ha például `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser` a és a, a felhasználó belekerül a naplóba, mert `audit_log_include_users` magasabb prioritással rendelkezik.

| **Esemény** | **Leírás** |
|---|---|
| `CONNECTION` | – A kapcsolatok kezdeményezése (sikeres vagy sikertelen) <br> -A felhasználó újrahitelesítése eltérő felhasználóval és jelszóval a munkamenet során <br> – A kapcsolatok megszakítása |
| `DML_SELECT`| Lekérdezések kiválasztása |
| `DML_NONSELECT` | Lekérdezések beszúrása/törlése/frissítése |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Lekérdezések, például "DROP DATABASE" |
| `DCL` | Lekérdezések, például "engedély megadása" |
| `ADMIN` | Lekérdezések, például "állapot megjelenítése" |
| `GENERAL` | Összes DML_SELECT, DML_NONSELECT, DML, DDL, DCL és ADMIN |
| `TABLE_ACCESS` | – Csak MySQL 5,7 esetén érhető el <br> – Táblázatos olvasási utasítások, például kijelölés vagy Beszúrás a következőbe:... Válassza <br> – Tábla-törlési utasítások, például törlés vagy TRUNCATE TABLE <br> – Táblázatos beszúrási utasítások, például INSERT vagy replace <br> – Táblázatos frissítési utasítások, például frissítés |

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

A naplófájlok integrálva vannak Azure Monitor diagnosztikai beállításokkal. Miután engedélyezte a naplók használatát a MySQL rugalmas kiszolgálón, kibocsáthatja őket Azure Monitor naplókba, Event Hubs vagy Azure Storage-ba. A diagnosztikai beállításokkal kapcsolatos további tudnivalókért tekintse meg a [diagnosztikai naplók dokumentációját](../../azure-monitor/essentials/platform-logs-overview.md). Ha többet szeretne megtudni arról, hogyan engedélyezheti a diagnosztikai beállításokat a Azure Portalban, tekintse meg a [log Portalt ismertető cikket](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

A következő szakaszok ismertetik a MySQL naplók kimenetét az esemény típusa alapján. A kimeneti módszertől függően a befoglalt mezők és a megjelenő sorrend eltérő lehet.

### <a name="connection"></a>Kapcsolat

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (csak MySQL 5,7 esetén érhető el) |
| `connection_id_d` | A MySQL által generált egyedi kapcsolatazonosító |
| `host_s` | Blank |
| `ip_s` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `db_s` | A következőhöz kapcsolódó adatbázis neve |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="general"></a>Általános

Az alábbi séma az általános, a DML_SELECT, a DML_NONSELECT, a DML, a DDL, a DCL és a rendszergazdai események típusára vonatkozik.

> [!NOTE]
> A esetében a rendszer `sql_text_s` csonkolja a naplót, ha az meghaladja a 2048 karaktert.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (csak MySQL 5,6 esetén érhető el) |
| `event_time` | Lekérdezés kezdési időpontja (UTC) időbélyegzővel |
| `error_code_d` | Hibakód, ha a lekérdezés nem sikerült. `0` nem jelent hibát |
| `thread_id_d` | A lekérdezést futtató szál azonosítója |
| `host_s` | Blank |
| `ip_s` | A MySQL-hez csatlakozó ügyfél IP-címe |
| `user_s` | A lekérdezést végrehajtó felhasználó neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

### <a name="table-access"></a>Tábla-hozzáférés

> [!NOTE]
> A Table Access-naplók csak a MySQL 5,7 kimenete.<br>A esetében a rendszer `sql_text_s` csonkolja a naplót, ha az meghaladja a 2048 karaktert.

| **Tulajdonság** | **Leírás** |
|---|---|
| `TenantId` | A bérlő azonosítója |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | A napló UTC-ben való rögzítésének időbélyegzője |
| `Type` | A napló típusa. Mindig `AzureDiagnostics` |
| `SubscriptionId` | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| `ResourceGroup` | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| `ResourceProvider` | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Erőforrás URI-ja |
| `Resource` | A kiszolgáló neve |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | A kiszolgáló neve |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` , `UPDATE` , vagy `DELETE` |
| `connection_id_d` | A MySQL által generált egyedi kapcsolatazonosító |
| `db_s` | Az elért adatbázis neve |
| `table_s` | Az elért tábla neve |
| `sql_text_s` | Teljes lekérdezés szövege |
| `\_ResourceId` | Erőforrás URI-ja |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Naplók elemzése Azure Monitor naplókban

Miután a naplókat a diagnosztikai naplókon keresztül Azure Monitor naplókra, a naplózott események további elemzését is elvégezheti. Az alábbiakban néhány példa olvasható, amely segítséget nyújt az első lépésekhez. Győződjön meg arról, hogy az alábbit frissíti a kiszolgáló nevével.

- Egy adott kiszolgálón lévő általános események listázása

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Egy adott kiszolgálón található kapcsolatok eseményeinek listázása

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Naplózott események összefoglalása egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- A naplózási esemény típusának eloszlása egy adott kiszolgálón

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Az összes MySQL-kiszolgáló naplózott eseményeinek listázása a naplók számára engedélyezett diagnosztikai naplókkal

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Következő lépések
- További információ a [lassú lekérdezési naplókról](concepts-slow-query-logs.md)
- Naplózási lekérdezési naplók konfigurálása a [Azure Portal](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->