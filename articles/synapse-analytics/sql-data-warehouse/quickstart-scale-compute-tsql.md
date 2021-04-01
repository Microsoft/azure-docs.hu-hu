---
title: 'Gyors útmutató: számítás skálázása dedikált SQL-készletben (korábban SQL DW) – T-SQL'
description: A számítási műveletek méretezése a dedikált SQL-készletben (korábbi nevén SQL DW) T-SQL és SQL Server Management Studio (SSMS) használatával. Bővítéssel a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ffffeb38aeb9d1f01f376d58a52323bb7b84b306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676323"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-t-sql"></a>Gyors útmutató: számítások méretezése dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben a T-SQL használatával

A számítási műveletek méretezése a dedikált SQL-készletben (korábbi nevén SQL DW) T-SQL és SQL Server Management Studio (SSMS) használatával. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) legújabb verzióját.

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Dedikált SQL-készlet létrehozása (korábban SQL DW)

A [Létrehozás és kapcsolódás – portál](create-data-warehouse-portal.md) használatával hozzon létre egy dedikált SQL-készletet (korábban SQL DW) **mySampleDataWarehouse** néven. Fejezze be a gyors üzembe helyezést, és győződjön meg arról, hogy van tűzfalszabály, és a dedikált SQL-készlethez (korábban SQL DW) tud csatlakozni SQL Server Management Studioon belülről.

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Server type (Kiszolgáló típusa) | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Íme egy példa: **mySampleDataWarehouseservername.database.Windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdafiók | Az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |

    ![Csatlakozás kiszolgálóhoz](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Kattintson a **Csatlakozás** gombra. Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDataWarehouse** elemet az új adatbázisban található objektumok megtekintéséhez.

    ![Adatbázis-objektumok](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Szolgáltatási cél megtekintése

A szolgáltatási cél beállítása a dedikált SQL-készlet (korábban SQL DW) adatraktár-egységeinek számát tartalmazza.

A dedikált SQL-készlet aktuális adattárház-egységének megtekintése (korábban SQL DW):

1. A **mySampleDataWarehouseservername.database.Windows.net**-hez való kapcsolódás alatt bontsa ki a **rendszeradatbázisok** csomópontot.
2. Kattintson a jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
3. Futtassa a következő lekérdezést a sys.database_service_objectives dinamikus felügyeleti nézetből való választáshoz.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. Az eredményben láthatja, hogy a **mySampleDataWarehouse** adattárház szolgáltatási célja „DW400”.

    ![nézet – aktuális – DWU](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Számítások méretezése

A dedikált SQL-készletben (korábban az SQL DW-ben) az adatraktár-egységek módosításával növelheti vagy csökkentheti a számítási erőforrásokat. A [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyorsútmutató létrehozta a **mySampleDataWarehouse** adattárházat, és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Kattintson a jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget.
2. Módosítsa a szolgáltatási célt az [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL-utasítással. Az alábbi lekérdezést futtatva például beállíthatja a DW300 szolgáltatási célt.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Méretmódosítási kérés monitorozása

Az előző módosítási kérés állapotának megtekintéséhez használhatja a `WAITFORDELAY` T-SQL szintaxist a sys.dm_operation_status dinamikus felügyeleti nézet (DMV) lekérdezéséhez.

A szolgáltatásobjektum módosítási állapotának lekérdezése:

1. Kattintson a jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget.
2. Futtassa az alábbi lekérdezést a sys.dm_operation_status DMV lekérdezéséhez.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. A kimenet az állapotlekérdezések naplóját jeleníti meg.

    ![Művelet állapota](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-dedicated-sql-pool-formerly-sql-dw-state"></a>Dedikált SQL-készlet (korábban SQL DW) állapotának keresése

Ha egy dedikált SQL-készlet (korábban SQL DW) szüneteltetve van, nem tud csatlakozni a T-SQL-hez. A dedikált SQL-készlet (korábban SQL DW) aktuális állapotának megtekintéséhez PowerShell-parancsmagot is használhat. Példaként tekintse meg a [DEDIKÁLT SQL Pool (korábbi nevén SQL DW) állapot – PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state)című témakört.

## <a name="check-operation-status"></a>Műveleti állapot ellenőrzése

A dedikált SQL-készleten (korábban SQL DW) lévő különböző felügyeleti műveletekkel kapcsolatos információk visszaküldéséhez futtassa a következő lekérdezést a [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) DMV-on. A lekérdezés például visszaadja a műveletet és annak állapotát, amely IN_PROGRESS, vagy COMPLETED lehet.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Következő lépések

Most megtanulta, hogyan méretezheti a számítási feladatait a dedikált SQL-készlethez (korábban SQL DW). Ha többet szeretne megtudni az Azure szinapszis Analytics szolgáltatásról, folytassa az információk betöltésére szolgáló oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adat betöltése egy dedikált SQL-készletbe](./load-data-from-azure-blob-storage-using-copy.md)