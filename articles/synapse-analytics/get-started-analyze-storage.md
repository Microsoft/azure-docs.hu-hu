---
title: 'Oktatóanyag: az első lépések a Storage-fiókokban lévő adatelemzés'
description: Ebből az oktatóanyagból megtudhatja, hogyan elemezheti a Storage-fiókban található információkat.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 71ba3d99ceee89464dafdf5bf4c16e70df146bef
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426076"
---
# <a name="analyze-data-in-a-storage-account"></a>A Storage-fiókban tárolt adatelemzés

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti a Storage-fiókban található információkat.

## <a name="overview"></a>Áttekintés

Eddig olyan forgatókönyveket ismertetünk, amelyekben az adatok a munkaterület adatbázisaiban találhatók. Most bemutatjuk, hogyan dolgozhat a fájlokkal a Storage-fiókokban. Ebben a forgatókönyvben a munkaterület és a munkaterület létrehozásakor megadott tároló elsődleges Storage-fiókját fogjuk használni.

* A Storage-fiók neve: **contosolake**
* A tároló neve a Storage-fiókban: **felhasználók**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>CSV-és Parquet-fájlok létrehozása a Storage-fiókban

Futtassa a következő kódot egy jegyzetfüzetben egy új kód cellában. Létrehoz egy CSV-fájlt és egy Parquet-fájlt a Storage-fiókban.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>A Storage-fiókban tárolt adatelemzés

A munkaterület alapértelmezett ADLS Gen2 fiókjában lévő adatait elemezheti, vagy egy ADLS Gen2-vagy blob Storage-fiókot a munkaterülethez társíthat "**Manage**" > "**társított szolgáltatások**" > "**New**" (az alábbi lépések az elsődleges ADLS Gen2 fiókra vonatkoznak).

1. A szinapszis Studióban nyissa meg **az** adatközpontot, majd kattintson a **csatolt** elemre.
1. Nyissa meg az Azure-beli **Gen2**  >  **sajátmunkaterület (Primary-contosolake)**.
1. Válassza a **felhasználók (elsődleges)** lehetőséget. Ekkor megjelenik a **NYCTaxi** mappa. A belsejében két, **PassengerCountStats_csvformat** és **PassengerCountStats_parquetformat** nevű mappát kell látnia.
1. Nyissa meg a **PassengerCountStats_parquetformat** mappát. A (z) rendszerben egy, a következőhöz hasonló nevű parketta-fájl jelenik meg: `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Kattintson a jobb gombbal a **. parketta** elemre, majd válassza az **új jegyzetfüzet** lehetőséget, majd válassza **a betöltés a DataFrame** lehetőséget. Egy új jegyzetfüzet jön létre a következőhöz hasonló cellával:

    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    display(df.limit(10))
    ```

1. Csatolja a **Spark1** nevű Spark-készlethez. Futtassa a cellát.
1. Kattintson a vissza a **felhasználók** mappára. Kattintson a jobb gombbal a **. Parque** fájlra, majd válassza az **új SQL-parancsfájl** lehetőséget, majd válassza a  >  **Top 100 sort**. A következőhöz hasonló SQL-parancsfájlt hoz létre:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    A parancsfájl ablakban ellenőrizze, hogy a **Kapcsolódás** mező a **beépített kiszolgáló nélküli SQL-** készletre van-e beállítva.

1. Futtassa a szkriptet.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tevékenységek koordinálása folyamatokkal](get-started-pipelines.md)
