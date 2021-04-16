---
title: 'Oktatóanyag: Adatok elemzése dedikált SQL-készletekkel – első lépések'
description: Ebben az oktatóanyagban a NYC Taxi mintaadatokkal fogja feltárni az SQL-készlet analytikus képességeit.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 0def1f957842417c3936e3f1c7bb5bc023109818
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536328"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Adatok elemzése dedikált SQL-készletekkel

Ebben az oktatóanyagban a NYC Taxi adatait fogja használni egy dedikált SQL-készlet képességeinek feltárására.

## <a name="create-a-dedicated-sql-pool"></a>Dedikált SQL-készlet létrehozása

1. A Synapse Studio bal oldali panelen válassza az  >  **SQL-készletek kezelése lehetőséget.**
1. Válassza az **Új lehetőséget**
1. Az **SQL-készlet neve mezőben válassza** az **SQLPOOL1 lehetőséget.**
1. A **Teljesítményszintnél válassza** a **DW100C lehetőséget**
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A dedikált SQL-készlet néhány percen belül elkészül. 

A dedikált SQL-készlet egy SQLPOOL1 nevű SQL-adatbázishoz **van társítva.**
1. Lépjen az **Adat-munkaterületre.**  >  
1. Egy **SQLPOOL1 nevű adatbázisnak kell lennie.** Ha nem látja, kattintson a **Frissítés gombra.**

A dedikált SQL-készlet számlázható erőforrásokat használ fel, ha az aktív. A költségek csökkentése érdekében később szüneteltetheti a készletet.

> [!NOTE] 
> Amikor új dedikált SQL-készletet (korábban SQL DW-t) hoz létre a munkaterületen, megnyílik a dedikált SQL-készlet üzembehelyezése lap. A kiépítés a logikai SQL-kiszolgálón történik.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>A NYC taxi adatainak betöltése az SQLPOOL1-be

1. A Synapse Studio keresse meg a **Develop** (Fejlesztés) központot, kattintson a gombra az új erőforrás hozzáadásához, majd hozzon **+** létre egy új SQL-szkriptet.
1. Válassza ki az "SQLPOOL1" készletet (amely az oktatóanyag [1.](./get-started-create-workspace.md) lépésében jött létre) a szkript felett található "Csatlakozás a következőhöz" legördülő listában.
1. Írja be a következő kódot:
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. A szkript végrehajtásához kattintson a Futtatás gombra.
1. A szkript kevesebb mint 60 másodperc alatt befejeződik. 2 millió sorNYI NYC Taxi-adatot tölt be egy dbo nevű **táblába. Trip (Utazás)**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>A NYC Taxi adatainak feltárása a dedikált SQL-készletben

1. A Synapse Studio az **Adatközpontba.**
1. Ugrás az **SQLPOOL1**  >  **táblákra.** 
3. Kattintson a jobb gombbal a **dbo-n. NYCTaxiTripSmall tábla,** majd az **Új SQL-szkript** kiválasztása  >  **Válassza a TOP 100 Rows lehetőséget.**
4. Várjon, amíg létrejön és fut egy új SQL-szkript.
5. Figyelje meg, hogy a **Connect to** SQL-szkript tetején automatikusan az **SQLPOOL1 nevű SQL-készlet van beállítva.**
6. Cserélje le az SQL-szkript szövegét erre a kódra, és futtassa.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Ez a lekérdezés bemutatja, hogy a teljes út és az átlagos út távolsága hogyan viszonyul az utasok számhoz.
1. Az SQL-szkript eredményablakában  módosítsa  a Nézetet Diagramra, hogy az eredményeket vonaldiagramként ábrázoló vizualizációt lássa.
    
## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok elemzése egy Azure Storage-fiókban](get-started-analyze-storage.md)
