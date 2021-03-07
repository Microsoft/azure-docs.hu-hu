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
ms.date: 12/31/2020
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423713"
---
# <a name="analyze-with-apache-spark"></a>Elemzés a Apache Spark

Ebből az oktatóanyagból megismerheti az adatok betöltését és elemzését az Azure szinapszis Apache Sparkával.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>NYC-taxi-adatelemzés a blob Storage-ban a Spark használatával

1. Az adatközpontban kattintson a  **+** gombra **egy új erőforrás hozzáadásához**, majd kattintson >> **tallózási** katalógus lehetőségre. 
1. Keresse meg a **New York-i taxi & limuzin Commission – Yellow taxi Trip Records** , és kattintson rá. 
1. A lap alján nyomja meg a **Continue (folytatás**) gombot, majd **adja hozzá az adatkészletet**. 
1. Egy pillanat elteltével az adatközpontban **kattintson a jobb** gombbal az **Azure Blob Storage >> minta adatkészletek >> nyc_tlc_yellow** elemre **, és** válassza az **új jegyzetfüzet** elemet, majd **töltse be az adatkeretbe**.
1. Ekkor létrejön egy új jegyzetfüzet a következő kóddal:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. A jegyzetfüzetben a **csatolás** menüben válassza ki a korábban létrehozott **Spark1** kiszolgáló nélküli Spark-készletet.
1. Válassza a **Futtatás** lehetőséget a cellában. A szinapszis egy új Spark-munkamenetet indít el, hogy szükség esetén futtassa ezt a cellát. Ha új Spark-munkamenetre van szükség, intially, hogy a rendszer két másodpercet fog létrehozni. 
1. Ha csak a dataframe sémáját szeretné megtekinteni, futtasson egy cellát a következő kóddal:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>A New York-i taxi-szolgáltatás betöltése a Spark nyctaxi-adatbázisba

Az adattábla a **SQLPOOL1** egyik táblájában érhető el. Töltse be egy **nyctaxi** nevű Spark-adatbázisba.

1. A szinapszis Studióban nyissa meg a **fejlesztés** központot.
1. Válassza a **+**  >  **Jegyzetfüzet** lehetőséget.
1. A jegyzetfüzet tetején állítsa a **csatolás** értéket **Spark1** értékre.
1. Az új jegyzetfüzet első kódjának cellájában, majd írja be a következő kódot:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Futtassa a szkriptet. Ez 2-3 percet is igénybe vehet.
1. Az adatközpont **munkaterület** lapján **kattintson a jobb** gombbal az **adatbázisok** elemre, majd válassza a **frissítés** lehetőséget. Ekkor látnia kell az adatbázis **nyctaxi (Spark)** a listában.


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>A New York-i taxi-adat elemzése a Spark és a notebook használatával

1. Térjen vissza a jegyzetfüzetbe.
1. Hozzon létre egy új kódlapot, és adja meg a következő kódot. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Futtassa a cellát, hogy megjelenjenek a **nyctaxi** Spark-adatbázisba betöltött NYC-taxik adatai.
1. Hozzon létre egy új kódlapot, és adja meg a következő kódot. Ezután futtassa a cellát ugyanazzal az elemzéssel, amelyet korábban a dedikált SQL Pool- **SQLPOOL1** adott meg. Ez a kód menti és megjeleníti az elemzés eredményeit egy **nyctaxi. passengercountstats** nevű táblába.


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

1. A cella eredményei között válassza a **diagram** lehetőséget az információk vizualizációjának megtekintéséhez.

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Adatok betöltése a Spark-táblából egy dedikált SQL Pool-táblába

Korábban a dedikált SQL Pool-táblázat **SQLPOOL1. dbo. Trip** fájlját másolta a Spark Table **nyctaxi. Trip**-ba. Ezután összesíti az adatokat a Spark Table **nyctaxi. passengercountstats**. Most másolja az **nyctaxi. passengercountstats** adatait egy dedikált SQL Pool-táblába, amelynek neve **SQLPOOL1. dbo. passengercountstats**.

1. Hozzon létre egy új kódlapot, és adja meg a következő kódot. Futtassa a cellát a jegyzetfüzetben. Az összesített Spark-táblázatot visszamásolja a dedikált SQL Pool-táblába.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatelemzés kiszolgáló nélküli SQL-készlettel](get-started-analyze-sql-on-demand.md)
