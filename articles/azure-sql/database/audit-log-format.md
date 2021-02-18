---
title: SQL Database napló formátuma
description: Megtudhatja, hogyan épülnek fel Azure SQL Database naplózási naplók.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572217"
---
# <a name="sql-database-audit-log-format"></a>SQL Database napló formátuma

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database a naplózás](auditing-overview.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat, vagy elküldi őket az Event hub-nak, vagy log Analytics az alsóbb rétegbeli feldolgozás és elemzés céljából.

## <a name="naming-conventions"></a>Elnevezési konvenciók

### <a name="blob-audit"></a>Blobok naplózása

Az Azure Blob Storage-ban tárolt naplók tárolása az `sqldbauditlogs` Azure Storage-fiókban megnevezett tárolóban történik. A tárolóban lévő címtár-hierarchia űrlap `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` . A blob fájlnevének formátuma a (z) `<CreationTime>_<FileNumberInSession>.xel` , ahol `CreationTime` UTC `hh_mm_ss_ms` formátumban van, és `FileNumberInSession` egy futó index abban az esetben, ha a munkamenet-naplók több blob-fájlba is kiterjednek.

Például a `Database1` `Server1` következő egy lehetséges érvényes elérési út az adatbázishoz:

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

A [csak olvasható replikák](read-scale-out.md) naplófájljai ugyanabban a tárolóban tárolódnak. A tárolóban lévő címtár-hierarchia űrlap `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` . A blob fájlneve ugyanazzal a formátummal van megosztva. A csak olvasható replikák naplófájljai ugyanabban a tárolóban tárolódnak.


### <a name="event-hub"></a>Eseményközpont

A naplózási eseményeket a rendszer az auditálási konfiguráció során meghatározott névtérre és az Event hub-ra írja, és az [Apache Avro](https://avro.apache.org/) -események törzsében rögzíti őket, és a JSON-formázással, UTF-8 kódolással tárolja őket. A naplók olvasásához használhat [Avro-eszközöket](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) vagy hasonló eszközöket, amelyek ezt a formátumot dolgozzák fel.

### <a name="log-analytics"></a>Log Analytics

A naplózási eseményeket a rendszer a naplózási konfiguráció során definiált Log Analytics munkaterületre írja az adott `AzureDiagnostics` kategóriába tartozó táblázatba `SQLSecurityAuditEvents` . További hasznos információk a Log Analytics keresési nyelvről és parancsokról: [log Analytics keresési referenciák](../../azure-monitor/logs/log-query-overview.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Naplózási napló mezői

| Név (blob) | Név (Event Hubs/Log Analytics) | Description | Blobtípus | Event Hubs/Log Analytics típusa |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | A művelet azonosítója | varchar (4) | sztring |
| action_name | action_name_s | A művelet neve | N/A | sztring |
| additional_information | additional_information_s | Az eseményről XML-ként tárolt további információk | nvarchar (4000) | sztring |
| affected_rows | affected_rows_d | A lekérdezés által érintett sorok száma | bigint | int |
| application_name | application_name_s| Ügyfélalkalmazás neve | nvarchar (128) | sztring |
| audit_schema_version | audit_schema_version_d | Mindig 1 | int | int |
| class_type | class_type_s | Naplózható entitás típusa, amelyen a naplózás bekövetkezik | varchar (2) | sztring |
| class_type_desc | class_type_description_s | A naplózás által bekövetkezett naplózható entitás leírása | N/A | sztring |
| client_ip | client_ip_s | Az ügyfélalkalmazás forrás IP-címe | nvarchar (128) | sztring |
| connection_id | N/A | A kiszolgálón belüli kapcsolatok azonosítója | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | A naplózott lekérdezés által visszaadott adattípusok és érzékenységi címkék az adatbázis minősített oszlopai alapján. További információ a [Azure SQL Database az adatfelderítésről és a besorolásról](data-discovery-and-classification-overview.md) | nvarchar (4000) | sztring |
| database_name | database_name_s | Az adatbázis-környezet, amelyben a művelet bekövetkezett | rendszerneve | sztring |
| database_principal_id | database_principal_id_d | Azon adatbázis-felhasználói környezet azonosítója, amelyet a művelet elvégez | int | int |
| database_principal_name | database_principal_name_s | Azon adatbázis-felhasználói környezet neve, amelyben a művelet el van hajtva | rendszerneve | sztring |
| duration_milliseconds | duration_milliseconds_d | Lekérdezés végrehajtásának időtartama (ezredmásodpercben) | bigint | int |
| event_time | event_time_t | A naplózható művelet elindításának dátuma és időpontja | datetime2 | dátum/idő |
| host_name | N/A | Ügyfél állomásneve | sztring | N/A |
| is_column_permission | is_column_permission_s | Jelző, amely azt jelzi, hogy ez egy oszlop szintű engedély. 1 = igaz, 0 = hamis | bit | sztring |
| N/A | is_server_level_audit_s | Jelző, amely azt jelzi, hogy a naplózás kiszolgálói szinten van-e | N/A | sztring |
| object_ azonosítója | object_id_d | Annak az entitásnak az azonosítója, amelyen a naplózás történt. Ide tartoznak a következők: kiszolgálói objektumok, adatbázisok, adatbázis-objektumok és séma-objektumok. 0, ha az entitás maga a kiszolgáló, vagy ha a naplózás nem egy objektum szintjén történik | int | int |
| object_name | object_name_s | Annak az entitásnak a neve, amelyen a naplózás történt. Ide tartoznak a következők: kiszolgálói objektumok, adatbázisok, adatbázis-objektumok és séma-objektumok. 0, ha az entitás maga a kiszolgáló, vagy ha a naplózás nem egy objektum szintjén történik | rendszerneve | sztring |
| permission_bitmask | permission_bitmask_s | Ha alkalmazható, megjeleníti a megadott, megtagadott vagy visszavont engedélyeket. | varbinary (16) | sztring |
| response_rows | response_rows_d | Az eredményhalmazban visszaadott sorok száma | bigint | int |
| schema_name | schema_name_s | Az a séma-környezet, amelyben a művelet bekövetkezett. NULL a sémán kívül előforduló naplózáshoz | rendszerneve | sztring |
| N/A | securable_class_type_s | Biztonságos objektum, amely leképezi a naplózott class_type | N/A | sztring |
| sequence_group_id | sequence_group_id_g | Egyedi azonosító | varbinary | GUID |
| sequence_number | sequence_number_d | Egy olyan naplózási rekordban lévő rekordok sorozatot követi nyomon, amely túl nagy volt ahhoz, hogy az írási pufferben a naplózáshoz illeszkedjen | int | int |
| server_instance_name | server_instance_name_s | Azon kiszolgálópéldány neve, ahol a naplózás történt | rendszerneve | sztring |
| server_principal_id | server_principal_id_d | Annak a bejelentkezési környezetnek az azonosítója, amelyben a művelet el van hajtva | int | int |
| server_principal_name | server_principal_name_s | Aktuális bejelentkezés | rendszerneve | sztring |
| server_principal_sid | server_principal_sid_s | Aktuális bejelentkezési SID | varbinary | sztring |
| session_id | session_id_d | Azon munkamenet azonosítója, amelyen az esemény bekövetkezett | smallint | int |
| session_server_principal_name | session_server_principal_name_s | A munkamenet kiszolgálójának résztvevője | rendszerneve | sztring |
| nyilatkozat | statement_s | Végrehajtott T-SQL-utasítás (ha van ilyen) | nvarchar (4000) | sztring |
| sikeres | succeeded_s | Azt jelzi, hogy az eseményt kiváltó művelet sikeres volt-e. A login és a Batch szolgáltatástól eltérő események esetén ez csak azt jelenti, hogy az engedély-ellenőrzési sikeres vagy sikertelen volt-e, nem a művelet. 1 = sikeres, 0 = sikertelen | bit | sztring |
| target_database_principal_id | target_database_principal_id_d | Az adatbázis rendszerbiztonsági tagja a GRANT/MEGTAGADÁS/visszavonás műveletet hajtja végre. 0, ha nem alkalmazható | int | int |
| target_database_principal_name | target_database_principal_name_s | A művelet célzott felhasználója. NULL, ha nem alkalmazható | sztring | sztring |
| target_server_principal_id | target_server_principal_id_d | Az a kiszolgáló, amelyen a megadási/megtagadási/visszavonási művelet végre lett hajtva. A 0 értéket adja vissza, ha nem alkalmazható | int | int |
| target_server_principal_name | target_server_principal_name_s | A művelet célhelyének bejelentkeznie. NULL, ha nem alkalmazható | rendszerneve | sztring |
| target_server_principal_sid | target_server_principal_sid_s | A célként megadott bejelentkezési azonosító. NULL, ha nem alkalmazható | varbinary | sztring |
| transaction_id | transaction_id_d | Csak SQL Server (2016-től kezdődően) – 0 Azure SQL Database | bigint | int |
| user_defined_event_id | user_defined_event_id_d | A felhasználó által definiált eseményazonosító a sp_audit_write argumentumként lett átadva. A rendszeresemények esetében NULL (alapértelmezett), a felhasználó által definiált esemény esetében nem nulla. További információ: [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | A felhasználó által megadott adatok sp_audit_write argumentumként lettek átadva. A rendszeresemények esetében NULL (alapértelmezett), a felhasználó által definiált esemény esetében nem nulla. További információ: [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | sztring |

## <a name="next-steps"></a>Következő lépések

További információ a [Azure SQL Database naplózásról](auditing-overview.md).