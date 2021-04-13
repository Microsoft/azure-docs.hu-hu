---
title: 'Oktatóanyag: A Tárfiókok adatainak elemzése – első lépések'
description: Ez az oktatóanyag bemutatja, hogyan elemezheti a tárfiókban található adatokat.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b88a7e6a9851018fce255fac0e39a30563b9bf4
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363835"
---
# <a name="analyze-data-in-a-storage-account"></a>Tárfiókban található adatok elemzése

Ez az oktatóanyag bemutatja, hogyan elemezheti a tárfiókban található adatokat.

## <a name="overview"></a>Áttekintés

Az eddig olyan forgatókönyveket fedte le, amelyekben az adatok a munkaterületen lévő adatbázisokban találhatók. Most megmutatjuk, hogyan dolgozhat a tárfiókok fájljaival. Ebben a forgatókönyvben a munkaterület és a tároló elsődleges tárfiókját fogjuk használni, amit a munkaterület létrehozásakor adtunk meg.

* A tárfiók neve: **contosolake**
* A tárfiókban található tároló neve: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>CSV- és Parquet-fájlok létrehozása a tárfiókban

Futtassa az alábbi kódot egy új kódcellában lévő jegyzetfüzetben. Létrehoz egy CSV-fájlt és egy Parquet-fájlt a tárfiókban.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Tárfiókban található adatok elemzése

Elemezheti ADLS Gen2 a munkaterület alapértelmezett ADLS Gen2-fiókjában lévő adatokat, vagy a **"Manage**" > "**Linked Services**" > "**New**" (Az alábbi lépések az elsődleges ADLS Gen2-fiókra vonatkoznak) használatával kapcsolhatók a munkaterülethez.

1. A Synapse Studio az **Adatközpontba,** majd válassza a Csatolt **lehetőséget.**
1. Ugrás a **Azure Data Lake Storage Gen2**  >  **(Elsődleges – contosolake) elemhez.**
1. Válassza **a users (Primary) (Felhasználók (elsődleges) lehetőséget.** Meg kell lennie a **NYCTaxi mappának.** A fájlban két mappát kell **látnia: PassengerCountStats_csvformat** és **PassengerCountStats_parquetformat.**
1. Nyissa meg **a PassengerCountStats_parquetformat** mappát. A fájlon belül egy parquet-fájl fog látni, például: `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Kattintson a jobb gombbal **a .parquet fájlra,** majd válassza az **Új jegyzetfüzet** lehetőséget, majd válassza **a Betöltés DataFrame-be lehetőséget.** A rendszer létrehoz egy új jegyzetfüzetet egy ehhez hasonló cellával:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. Csatolja a Spark1 nevű **Spark-készletet.** Futtassa a cellát.
1. Válassza a vissza lehetőséget **a users (felhasználók) mappába.** Kattintson ismét a jobb gombbal a **.parquet-fájlra,** majd válassza az Új **SQL-szkript**  >  **SELECT TOP 100 rows (ELSŐ 100 sor kiválasztása) lehetőséget.** A következőhöz hasonló SQL-szkriptet hoz létre:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    A szkriptablakban győződjön meg arról, hogy a **Csatlakozás** a következőhöz mező a Beépített kiszolgáló nélküli **SQL-készletre** van beállítva.

1. Futtassa a szkriptet.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tevékenységek vezénylelése folyamatokkal](get-started-pipelines.md)
