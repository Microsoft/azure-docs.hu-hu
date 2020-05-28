---
title: Mappák és több CSV-fájl lekérdezése az SQL igény szerinti használatával (előzetes verzió)
description: Az SQL on-demand (előzetes verzió) támogatja több fájl/mappa olvasását helyettesítő karakterekkel, amelyek hasonlóak a Windows operációs rendszerben használt helyettesítő karakterekhez.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bb5c01bac512504fc6bee52be7cf619f29bdf959
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117184"
---
# <a name="query-folders-and-multiple-csv-files"></a>Mappák és több CSV-fájl lekérdezése  

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analytics szolgáltatásban.

Az SQL on-demand támogatja több fájl/mappa olvasását helyettesítő karakterek használatával, amelyek a Windows operációs rendszerben használt helyettesítő karakterekhez hasonlóak. Ez azonban nagyobb rugalmasságot jelent, mivel több helyettesítő karakter is engedélyezett.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** , amelyen végre fogja hajtani a lekérdezéseket. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

A minta lekérdezések követéséhez a *CSV/taxi* mappát kell használnia. A New York-i, a sárga taxis utazás a következő adatokat tartalmazza: július 2016 és június 2018. A *CSV/taxiban* található fájlokat a következő minta alapján nevezi el az év és hónap után: yellow_tripdata_ <year> - <month> . csv
        
Minden fájl a következő szerkezettel rendelkezik:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>A mappában található összes fájl olvasása
    
Az alábbi példa az összes NYC Yellow taxi-adatfájlt beolvassa a *CSV/taxi* mappából, és az utasok és a túrák számát adja vissza évente. Emellett az összesítő függvények használatát is megjeleníti.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET elért összes fájlnak ugyanazzal a struktúrával kell rendelkeznie (például oszlopok és adattípusok száma).

### <a name="read-subset-of-files-in-folder"></a>Fájlok részhalmazának olvasása a mappában

Az alábbi példa beolvassa az 2017 NYC Yellow taxi adatfájljait a *CSV/taxi* mappából a helyettesítő karakter használatával, és a teljes viteldíj összegét adja vissza a fizetési típusok száma szerint.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Az egyetlen OPENROWSET elért összes fájlnak ugyanazzal a struktúrával kell rendelkeznie (például oszlopok és adattípusok száma).

## <a name="read-folders"></a>Mappák olvasása

A OPENROWSET számára megadott elérési út lehet egy mappa elérési útja is. A következő fejezetek tartalmazzák ezeket a lekérdezési típusokat.

### <a name="read-all-files-from-specific-folder"></a>Az összes fájl olvasása adott mappából

A mappában lévő összes fájlt elolvashatja a fájl szintű helyettesítő karakterrel, ahogy az az [összes fájl olvasása a mappában](#read-all-files-in-folder)lehetőség látható. Azonban lehetőség van egy mappa lekérdezésére és az abban a mappában található összes fájl felhasználására.

Ha a OPENROWSET-ben megadott elérési út egy mappára mutat, a rendszer a mappában lévő összes fájlt a lekérdezés forrásaként fogja használni. A következő lekérdezés a *CSV/taxi* mappában található összes fájlt beolvassa.

> [!NOTE]
> Jegyezze fel az elérési út végén található/végét az alábbi lekérdezésben. Egy mappát jelöl. Ha a/ki van hagyva, a lekérdezés a *taxi* nevű fájlt célozza meg helyette.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET elért összes fájlnak ugyanazzal a struktúrával kell rendelkeznie (például oszlopok és adattípusok száma).

### <a name="read-all-files-from-multiple-folders"></a>Több mappából származó összes fájl olvasása

A fájlok több mappából is olvashatók a helyettesítő karakterek használatával. A következő lekérdezés beolvassa az összes olyan fájlt, amely a *CSV* mappában található összes mappából a *t* és a t végződéssel kezdődő neveket *tartalmaz.*

> [!NOTE]
> Jegyezze fel az elérési út végén található/végét az alábbi lekérdezésben. Egy mappát jelöl. Ha a/ki van hagyva, a lekérdezés a " *t &ast; * " nevű fájlokat fogja megcélozni.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET elért összes fájlnak ugyanazzal a struktúrával kell rendelkeznie (például oszlopok és adattípusok száma).

Mivel csak egy olyan mappa van, amely megfelel a feltételeknek, a lekérdezés eredménye megegyezik a [mappában található összes fájl olvasásával](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Több helyettesítő karakter

Több helyettesítő karaktert is használhat a különböző elérési utak szintjén. Például a korábbi lekérdezések gazdagabbá tehetők csak a 2017 adatokkal rendelkező fájlok olvasására, az összes olyan mappából, amelynél a nevek a *t* értékkel kezdődnek, és az *i*-vel végződik.

> [!NOTE]
> Jegyezze fel az elérési út végén található/végét az alábbi lekérdezésben. Egy mappát jelöl. Ha a/ki van hagyva, a lekérdezés a " *t &ast; * " nevű fájlokat fogja megcélozni.
> Lekérdezés legfeljebb 10 helyettesítő karakterből áll.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET elért összes fájlnak ugyanazzal a struktúrával kell rendelkeznie (például oszlopok és adattípusok száma).

Mivel csak egy olyan mappája van, amely megfelel a feltételeknek, a lekérdezés eredménye ugyanaz, mint a [mappában található fájlok olvasási részhalmaza](#read-subset-of-files-in-folder) , és az [összes fájl beolvasása az adott mappából](#read-all-files-from-specific-folder). Összetettebb helyettesítő karakteres használati forgatókönyvek találhatók a [lekérdezési parketta fájljaiban](query-parquet-files.md).

## <a name="next-steps"></a>További lépések

További információt a [lekérdezés-specifikus fájlok](query-specific-files.md) című cikkben talál.
