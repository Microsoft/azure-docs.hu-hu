---
title: 'Oktatóanyag: az adatelemzés első lépései dedikált SQL-készletekkel'
description: Ebben az oktatóanyagban a New York-i taxi-mintaadatok segítségével megismerheti az SQL-készlet analitikus funkcióit.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 4588eee721a58a7e4f3366d0d325b48de0f56ae5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259812"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Az adatelemzés dedikált SQL-készletekkel

Ebben az oktatóanyagban a New York-i taxi-adataival felhasználhatja a dedikált SQL-készlet képességeit.

## <a name="create-a-dedicated-sql-pool"></a>Dedikált SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza az   >  **SQL-készletek** kezelése lehetőséget.
1. **Új** kiválasztása
1. Az **SQL-készlet neve** területen válassza a **SQLPOOL1** lehetőséget.
1. A **teljesítmény szintjén** válassza a **DW100C** lehetőséget.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A dedikált SQL-készlet néhány percen belül elkészül. 

A dedikált SQL-készlet egy **SQLPOOL1** néven is ismert SQL-adatbázishoz van társítva.
1. Navigáljon az  >  **adatmunkaterület** elemre.
1. Ekkor meg kell jelennie egy **SQLPOOL1** nevű adatbázisnak. Ha nem látja, kattintson a **frissítés** gombra.

A dedikált SQL-készletek számlázható erőforrásokat használnak, amíg aktívak. A készletet később is szüneteltetheti a költségek csökkentése érdekében.

> [!NOTE] 
> Amikor új dedikált SQL-készletet (korábban SQL DW-t) hoz létre a munkaterületen, megnyílik a dedikált SQL Pool kiépítési oldal. A kiépítés a logikai SQL-kiszolgálón történik.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>A New York-i taxi-szolgáltatás betöltése a SQLPOOL1

1. A szinapszis Studióban navigáljon a **fejlesztés** hubhoz, kattintson a **+** gombra új erőforrás hozzáadásához, majd hozzon létre új SQL-parancsfájlt.
1. Válassza ki a "SQLPOOL1" készletet (az oktatóanyag [1. lépésében](./get-started-create-workspace.md) létrehozott készletet) a parancsfájl feletti "kapcsolódás" legördülő listához.
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

    --Uncomment the 4 lines below to create a stored procedure for data pipeline orchestration
    --CREATE PROC bulk_load_NYCTaxiTripSmall
    --AS
    --BEGIN
    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5, PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. A szkript végrehajtásához kattintson a Futtatás gombra.
1. Ez a szkript 60 másodpercnél rövidebb ideig tart. Egy dbo nevű táblába tölti be a New York-i, 2 000 000-as sorokat **. Utazás**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>A New York-i taxi adatai a dedikált SQL-készletben

1. A szinapszis Studióban nyissa meg **az** adatközpontot.
1. Nyissa meg a **SQLPOOL1**  >  **táblákat**. 
3. Kattintson a jobb gombbal a **dbo. Utazási** táblázat és válassza az **új SQL-parancsfájl** lehetőséget, majd  >  **válassza a Top 100 sort**.
4. Várjon, amíg a rendszer létrehoz egy új SQL-parancsfájlt, és futtatja azt.
5. Figyelje meg, hogy az SQL-parancsfájl felső részén a **Kapcsolódás** automatikusan a **SQLPOOL1** nevű SQL-készletre lesz beállítva.
6. Cserélje le az SQL-parancsfájl szövegét ezzel a kóddal, majd futtassa.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Ez a lekérdezés azt mutatja be, hogy a teljes utazási távolság és az átlagos utazási távolság az utasok számával függ.
1. Az SQL-parancsfájl eredményének ablakában módosítsa  a nézetet **diagramra** , hogy megjelenítse az eredmények vonal diagramként való megjelenítését.
    
## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Storage-fiókban tárolt adatelemzés](get-started-analyze-storage.md)
