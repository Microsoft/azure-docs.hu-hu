---
title: XEvent gyűrűs puffer kódja
description: Egy Transact-SQL-kódrészletet biztosít, amely egyszerűen és gyorsan használható a gyűrűs puffer céljának használatával Azure SQL Databaseban.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487303"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>A kibővített eseményekhez tartozó gyűrűs pufferek Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

A tesztelés során egy teljes kódrészletet szeretne használni a kibővített eseményekre vonatkozó információk rögzítéséhez és jelentéséhez. A kiterjesztett események legkönnyebben megcélozható a [gyűrűs puffer célja](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

Ez a témakör egy Transact-SQL-kód mintát mutat be, amely a következőket tartalmazza:

1. Létrehoz egy táblázatot, amely a következővel szemlélteti az-t:.
2. Munkamenetet hoz létre egy meglévő bővített eseményhez, azaz **SQLServer.sql_statement_starting**.

   * Az esemény olyan SQL-utasításokra korlátozódik, amelyek egy adott frissítési karakterláncot tartalmaznak: a **(z) "% Update tabEmployee%" utasítást**.
   * Úgy dönt, hogy elküldi az esemény kimenetét egy gyűrűs puffer típusú célnak, azaz  **package0.ring_buffer**.
3. Elindítja az esemény-munkamenetet.
4. Néhány egyszerű SQL UPDATE-utasítást is kiad.
5. Egy SQL SELECT utasítás kiírása az esemény kimenetének a gyűrűs pufferből való lekéréséhez.

   * **sys.dm_xe_database_session_targets** és más dinamikus felügyeleti nézetek (DMV-EK) vannak csatlakoztatva.
6. Leállítja az esemény-munkamenetet.
7. Eldobja a gyűrűs puffer célját, hogy felszabadítsa az erőforrásait.
8. Eldobja az esemény-munkamenetet és a bemutató táblát.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Bármely adatbázis, amelyben létrehozhat egy táblát.
  
  * Igény szerint percek alatt [létrehozhat egy **AdventureWorksLT** bemutató adatbázist](single-database-create-quickstart.md) .
* SQL Server Management Studio (ssms.exe), ideális esetben a legújabb havi frissítési verzió.
  A legújabb ssms.exe a következő címről töltheti le:
  
  * A című témakör a [letöltés SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  * [Közvetlen hivatkozás a letöltésre.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kódminta

Nagyon kicsi módosítás esetén a következő gyűrűs puffer kód Azure SQL Database vagy Microsoft SQL Server is futtatható. A különbség a (z) "_database" csomópont jelenléte a (z) 5. lépésben a FROM záradékban használt dinamikus felügyeleti nézetek (DMV) nevében. Például:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Gyűrű pufferének tartalma

`ssms.exe`A kód futtatására szolgáló mintát használtuk.

Az eredmények megtekintéséhez a **target_data_XML** oszlop fejléc alatti cellára kattintott.

Ezután az eredmények ablaktáblán rákattintott a cellára **target_data_XML** oszlop fejlécében. Ekkor kattintson a létrehozott egy másik fájl fülre a ssms.exe, amelyben az eredmény cellájának tartalma XML-ként jelenik meg.

A kimenet az alábbi blokkban látható. Úgy néz ki, hogy hosszú, de csak két **\<event>** elemből áll.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>A gyűrű puffere által tárolt kiadási erőforrások

Ha befejezte a gyűrűs puffert, eltávolíthatja azt, és felszabadíthatja az általa kiadott erőforrásokat a következőhöz **hasonló módon** :

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

Az esemény-munkamenet definíciója frissül, de nem kerül eldobásra. Később a gyűrűs puffer egy másik példányát is hozzáadhatja az esemény-munkamenethez:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>További információ

Azure SQL Database a kiterjesztett események elsődleges témája:

* [Kibővített eseményekkel kapcsolatos megfontolások a Azure SQL Databaseban](xevent-db-diff-from-svr.md), amely a Azure SQL Database és a Microsoft SQL Server közötti különbségeket a kiterjesztett események bizonyos aspektusaival szemben.

A kiterjesztett eseményekre vonatkozó egyéb mintakód-témakörök a következő hivatkozásokon érhetők el. A mintavételt azonban rendszeresen ellenőriznie kell, hogy látható-e a minta célja Microsoft SQL Server szemben Azure SQL Database. Ezután eldöntheti, hogy szükség van-e kisebb módosításokra a minta futtatásához.

* Mintakód a Azure SQL Database számára: az [eseménynaplóban a kiterjesztett események Azure SQL Database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->