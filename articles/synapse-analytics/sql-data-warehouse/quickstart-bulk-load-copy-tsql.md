---
title: 'Rövid útmutató: Adatok tömeges betöltése egyetlen T-SQL-utasítással'
description: Adatok tömeges betöltése a COPY utasítással
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2b41342ab7d267c37b8e68fdbcaa9d570034ac17
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568437"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Rövid útmutató: Adatok tömeges betöltése a COPY utasítással

Ebben a rövid útmutatóban tömegesen tölt be adatokat a dedikált SQL-készletbe az egyszerű és rugalmas [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítással a nagy átviteli sebességű adatbetöltéshez. A COPY utasítás az ajánlott betöltési segédprogram, mivel lehetővé teszi az adatok zökkenőmentes és rugalmas betöltését a következő funkciók biztosításával:

- Alacsonyabb jogosultságú felhasználók betöltésének engedélyezése szigorú CONTROL engedélyek nélkül az adattárházban
- Egyetlen T-SQL-utasítás használata további adatbázis-objektumok létrehozása nélkül
- A tárfiókkulcsok közös hozzáférésű jogosultságok (SAS) használatával való felfedése nélkül kihasználhatja a finomabb engedélymodellt
- Adjon meg egy másik tárfiókot az ERRORFILE helyhez (REJECTED_ROW_LOCATION)
- Az egyes céloszlopok alapértelmezett értékeinek testreszabása és forrásadatmezők megadása adott céloszlopba való betöltéshez
- Egyéni sorterminátor megadása CSV-fájlokhoz
- A CSV-fájlok escape-sztring-, mező- és sorelválasztói
- A SQL Server formátumok kihasználása CSV-fájlokhoz
- Helyettesítő karakterek és több fájl megadása a tárolási hely elérési útján

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy dedikált SQL-készlettal. Ha még nem hozott létre dedikált SQL-készletet, használja a Létrehozás és csatlakozás – portál gyorsútmutatót. [](create-data-warehouse-portal.md)

## <a name="set-up-the-required-permissions"></a>A szükséges engedélyek beállítása

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>A céltábla létrehozása

Ebben a példában a New York-i taxi adatkészletből töltünk be adatokat. Betöltünk egy Trip (Utazás) nevű táblát, amely az egy év alatt tett taxis utakat jelöli. A tábla létrehozásához futtassa a következőt:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>A COPY utasítás futtatása

Futtassa a következő COPY utasítást, amely betölti az adatokat az Azure Blob Storage-fiókból a Trip táblába.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>A terhelés figyelése

A következő lekérdezés rendszeres futtatásával ellenőrizze, hogy a terhelés halad-e:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Következő lépések

- Az adatok betöltésével kapcsolatos ajánlott eljárásokért lásd: Ajánlott eljárások [az adatok betöltéséhez.](./guidance-for-loading-data.md)
- További információ az adatbeterhelések erőforrásainak kezeléséhez: Számítási [feladatok elkülönítése.](./quickstart-configure-workload-isolation-tsql.md)