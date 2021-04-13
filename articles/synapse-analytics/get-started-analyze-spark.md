---
title: 'Rövid útmutató: a Spark elemzésének első lépései'
description: Ebből az oktatóanyagból megismerheti, hogyan elemezheti az adatApache Sparkt.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 2b85fe21fee34a9bedab33f0d10756bbfe8dc88b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305208"
---
# <a name="analyze-with-apache-spark"></a>Elemzés a Apache Spark

Ebből az oktatóanyagból megismerheti az adatok betöltését és elemzését az Azure szinapszis Apache Sparkával.

## <a name="create-a-serverless-apache-spark-pool"></a>Kiszolgáló nélküli Apache Spark készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza a   >  **Apache Spark készletek** kezelése lehetőséget.
1. **Új** kiválasztása 
1. **Apache Spark a készlet neve** mezőbe írja be a következőt: **Spark1**.
1. A **csomópont mérete** mezőben adja meg a **kis** értéket.
1. Ha a **csomópontok száma** a minimum 3 és a maximum 3 értéket állítja be
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A Apache Spark-készlet néhány másodpercen belül elkészül.

## <a name="understanding-serverless-apache-spark-pools"></a>A kiszolgáló nélküli Apache Spark készletek ismertetése

A kiszolgáló nélküli Spark-készletek segítségével azt jelezheti, hogy egy felhasználó hogyan kíván dolgozni a Spark használatával. Ha egy készletet kezd használni, a rendszer létrehozza a Spark-munkamenetet, ha szükséges. A készlet azt szabályozza, hogy a munkamenet hány Spark-erőforrást fog használni, és hogy mennyi ideig tart a munkamenet az automatikus szüneteltetés előtt. Az adott munkamenet során használt Spark-erőforrásokért kell fizetnie, nem maga a készlet. Ily módon a Spark-készletek lehetővé teszik a Spark használatával végzett munkát anélkül, hogy aggódnia kellene a fürtök kezelésében. Ez hasonló a kiszolgáló nélküli SQL-készlet működéséhez.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>A New York-i taxi-adatelemzés a Spark-készlettel

1. A szinapszis Studióban nyissa meg a **fejlesztés** hubot
2. Új jegyzetfüzet létrehozása
3. Hozzon létre egy új kódlapot, és illessze be a következő kódot a cellába.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. A jegyzetfüzetben a **csatolás** menüben válassza ki a korábban létrehozott **Spark1** kiszolgáló nélküli Spark-készletet.
1. Válassza a **Futtatás** lehetőséget a cellában. A szinapszis egy új Spark-munkamenetet indít el, hogy szükség esetén futtassa ezt a cellát. Ha új Spark-munkamenetre van szükség, a rendszer először a létrehozás előtt két másodpercet vesz igénybe. 
1. Ha csak a dataframe sémáját szeretné megtekinteni, futtasson egy cellát a következő kóddal:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>A New York-i taxi-szolgáltatás betöltése a Spark nyctaxi-adatbázisba

Az adatszolgáltatás a **DF** nevű dataframe keresztül érhető el. Töltse be egy **nyctaxi** nevű Spark-adatbázisba.

1. Vegyen fel egy új kódlapot a jegyzetfüzetbe, majd írja be a következő kódot:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

1. Hozzon létre egy új kódlapot, és adja meg a következő kódot. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Futtassa a cellát, hogy megjelenjenek a **nyctaxi** Spark-adatbázisba betöltött NYC-taxik adatai.
1. Hozzon létre egy új kódlapot, és adja meg a következő kódot. Ezeket az adatelemzéseket elemezzük, és az eredményeket egy **nyctaxi. passengercountstats** nevű táblába mentjük.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistance) as SumTripDistance,
          AVG(TripDistance) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistance > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. A cella eredményei között válassza a **diagram** lehetőséget az információk vizualizációjának megtekintéséhez.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatelemzés dedikált SQL-készlettel](get-started-analyze-sql-pool.md)
