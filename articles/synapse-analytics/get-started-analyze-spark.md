---
title: 'Rövid útmutató: Az elemzés első lépések a Sparkkal'
description: Ebben az oktatóanyagban megtanulja, hogyan elemezhet adatokat Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: de48f906f4dc86bf6297cfb3b76f406df49feec3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363852"
---
# <a name="analyze-with-apache-spark"></a>Elemzés Apache Spark

Ebben az oktatóanyagban megismerheti az adatok betöltésének és elemzésének alapvető lépéseit a Apache Spark és Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Kiszolgáló nélküli adatbáziskészlet Apache Spark létrehozása

1. A Synapse Studio bal oldali panelen válassza a **Manage** Apache Spark pools (Készlet  >  **kezelése) lehetőséget.**
1. Válassza az **Új lehetőséget** 
1. A **Apache Spark neveként adja meg a** **Spark1 nevet.**
1. A **Csomópont méreteként adja meg** a Small **(Kicsi) méretet.**
1. A **Csomópontok száma beállításhoz** állítsa a minimumot 3-ra, a maximumot pedig 3-ra.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A Apache Spark néhány másodpercen belül elkészül.

## <a name="understanding-serverless-apache-spark-pools"></a>A kiszolgáló nélküli Apache Spark ismertetése

A kiszolgáló nélküli Spark-készlet azt jelzi, hogy a felhasználó hogyan szeretne dolgozni a Sparkkal. Amikor elkezdi használni a készletet, szükség esetén létrejön egy Spark-munkamenet. A készlet azt szabályozza, hogy az a munkamenet hány Spark-erőforrást használ, és mennyi ideig tart a munkamenet, mielőtt az automatikusan szünetel. Az ebben a munkamenetben használt Spark-erőforrásokért nem a készletért kell fizetnie. Így a Spark-készlet lehetővé teszi a Sparkkal való munkát anélkül, hogy fürtök kezelésével kellene törődni. Ez hasonló a kiszolgáló nélküli SQL-készlethez.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>NYC Taxi-adatok elemzése Spark-készletben

1. A Synapse Studio a Develop **(Fejlesztés) központba**
2. Új jegyzetfüzet létrehozása
3. Hozzon létre egy új kódcellát, és illessze be a következő kódot a cellába.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. A jegyzetfüzet Csatolás **menüjében** válassza ki a korábban létrehozott kiszolgáló nélküli **Spark1** Spark-készletet.
1. Válassza **a Futtatás** a cellán lehetőséget. A Synapse elindít egy új Spark-munkamenetet, amely szükség esetén futtatja ezt a cellát. Ha új Spark-munkamenetre van szükség, a létrehozás kezdetben körülbelül két másodpercet fog igénybe venni. 
1. Ha csak az adatkeret sémáját szeretné látni, futtason egy cellát az alábbi kóddal:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>A NYC Taxi adatainak betöltése a Spark nyctaxi adatbázisába

Az adatok a df nevű **adatkereten keresztül érhetők el.** Töltse be egy **nyctaxi nevű Spark-adatbázisba.**

1. Adjon hozzá egy új kódcellát a jegyzetfüzethez, majd írja be a következő kódot:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>NyC Taxi adatainak elemzése Spark és notebookok használatával

1. Hozzon létre egy új kódcellát, és írja be a következő kódot. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Futtassa a cellát a **nyctaxi** Spark-adatbázisba betöltött NYC taxiadatok megjelenítése érdekében.
1. Hozzon létre egy új kódcellát, és írja be a következő kódot. Az adatokat elemzés után egy **nyctaxi.passengercountstats** nevű táblába mentjük.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. A cellaeredmények között válassza a **Diagram lehetőséget** a vizualizációs adatok megjelenítéséhez.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok elemzése dedikált SQL-készlet használatával](get-started-analyze-sql-pool.md)
