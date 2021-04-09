---
title: A Apache Spark használatával olvashatja és írhatja az adatAzure SQL Database
description: Ismerje meg, hogyan állíthat be kapcsolatot a HDInsight Spark-fürt és a Azure SQL Database között. Az adatolvasáshoz, az adatíráshoz és az adatfolyamok SQL-adatbázisba való írásához
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c9c3b2d915e54cf954703c56c2087637cc80aa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864618"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Az HDInsight Spark-fürt használata az Azure SQL Databaseba való adatolvasásra és írásra

Megtudhatja, hogyan csatlakoztatható Apache Spark-fürt az Azure HDInsight-ben Azure SQL Database használatával. Ezután olvassa el, írja és továbbítsa az adatátvitelt az SQL Database-be. A cikkben szereplő utasítások egy Jupyter Notebook használatával futtatják a Scala-kódrészleteket. Létrehozhat azonban egy önálló alkalmazást a Scalaben vagy a Pythonban, és ugyanezeket a feladatokat is végrehajthatja.

## <a name="prerequisites"></a>Előfeltételek

* Azure HDInsight Spark-fürt.  Kövesse az [Apache Spark-fürt létrehozása a HDInsight-ben](apache-spark-jupyter-spark-sql.md)című témakör utasításait.

* Azure SQL Database. Kövesse az [adatbázis létrehozása Azure SQL Databaseban](../../azure-sql/database/single-database-create-quickstart.md)című témakör utasításait. Győződjön meg arról, hogy létrehoz egy adatbázist a minta **AdventureWorksLT** sémával és az adattal. Győződjön meg arról is, hogy egy kiszolgálói szintű tűzfalszabály létrehozása lehetővé teszi az ügyfél IP-címének elérését az SQL-adatbázishoz. A tűzfalszabály hozzáadására vonatkozó utasítások ugyanabban a cikkben találhatók. Miután létrehozta az SQL-adatbázist, ügyeljen rá, hogy a következő értékeket érdemes megtartania. Szüksége van rájuk, hogy egy Spark-fürtből csatlakozhasson az adatbázishoz.

    * A kiszolgáló neve.
    * Az adatbázis neve.
    * Azure SQL Database rendszergazdai Felhasználónév/jelszó.

* SQL Server Management Studio (SSMS). Kövesse az [SSMS használata a kapcsolódáshoz és az adatlekérdezéshez](../../azure-sql/database/connect-query-ssms.md)című témakör utasításait.

## <a name="create-a-jupyter-notebook"></a>Jupyter-notebook létrehozása

Kezdje a Spark-fürthöz társított Jupyter Notebook létrehozásával. Ezt a jegyzetfüzetet használja a cikkben használt kódrészletek futtatásához.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fürtöt.
1. A jobb oldalon válassza ki **Jupyter notebook** a **fürt irányítópultok** alatt.  Ha nem látja a **fürt irányítópultját**, válassza az **Áttekintés** lehetőséget a bal oldali menüben. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png " alt-text="Jupyter Notebook on Apache Spark" border="true":::

   > [!NOTE]  
   > A Spark-fürt Jupyter Notebook a böngészőben a következő URL-cím megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** nevet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. A Jupyter Notebook a jobb felső sarokban kattintson az **új** elemre, majd a **Spark** elemre a Scala notebook létrehozásához. A HDInsight Spark-fürtön található Jupyter-jegyzetfüzetek a Python2-alkalmazások **PySpark** -kernelét, valamint a PySpark3-alkalmazások **Python3** -kernelét is biztosítják. Ebben a cikkben egy Scala-jegyzetfüzetet hozunk létre.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png " alt-text="Kernelek Jupyter Notebook a Sparkban" border="true":::

    További információ a kernelekről: [Jupyter notebook kernelek használata Apache Spark-fürtökkel a HDInsight-ben](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Ebben a cikkben a Spark (Scala) kernelt használjuk, mert a Sparkból a SQL Databaseba érkező adatfolyam-adatok csak a Scala és a Java szolgáltatásban támogatottak. Bár az SQL-re való olvasás és az SQL-be való írás a Python használatával végezhető el, az ebben a cikkben szereplő konzisztencia érdekében mindhárom művelethez a Scalat használjuk.

1. Egy új jegyzetfüzet alapértelmezett névvel nyílik meg, **névtelenül**. Kattintson a jegyzetfüzet nevére, és adjon meg egy tetszőleges nevet.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png " alt-text="A notebook elnevezése" border="true":::

Most már megkezdheti az alkalmazás létrehozását.

## <a name="read-data-from-azure-sql-database"></a>Adatok beolvasása a Azure SQL Databaseból

Ebben a szakaszban egy tábla (például **SalesLT. címe**) adatait olvassa be a AdventureWorks-adatbázisban.

1. Egy új Jupyter Notebook a kód cellában illessze be a következő kódrészletet, és cserélje le a helyőrző értékeket az adatbázis értékeire.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlettel hozzon létre egy JDBC URL-címet, amelyet át tud adni a Spark dataframe API-khoz. A kód létrehoz egy `Properties` objektumot, amely a paramétereket fogja tárolni. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Az alábbi kódrészlet használatával hozzon létre egy dataframe az adatbázisban lévő táblából származó adatokkal. Ebben a kódrészletben egy olyan táblát használunk, `SalesLT.Address` amely a **AdventureWorksLT** -adatbázis részeként érhető el. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Mostantól műveleteket végezhet a dataframe, például lekérdezheti az adatsémát:

    ```scala
    sqlTableDF.printSchema
    ```

    A következő képhez hasonló kimenet jelenik meg:

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png " alt-text="séma kimenete" border="true":::

1. Olyan műveleteket is végrehajthat, mint például az első 10 sor beolvasása.

    ```scala
    sqlTableDF.show(10)
    ```

1. Vagy lekérhet bizonyos oszlopokat az adatkészletből.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Adatírás az Azure SQL Databaseba

Ebben a szakaszban egy CSV-fájlt használunk a fürtben, amely egy táblázatot hoz létre az adatbázisban, és feltölti azokat az adatokkal. A minta CSV-fájl (**HVAC.csv**) az összes HDInsight-fürtön elérhető a következő helyen: `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. Egy új Jupyter Notebook a kód cellában illessze be a következő kódrészletet, és cserélje le a helyőrző értékeket az adatbázis értékeire.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlet egy JDBC URL-címet hoz létre, amelyet átadhat a Spark dataframe API-khoz. A kód létrehoz egy `Properties` objektumot, amely a paramétereket fogja tárolni. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. A következő kódrészlettel kibonthatja a HVAC.csv lévő adatok sémáját, és a séma használatával betöltheti a CSV-fájlból az dataframe-ben tárolt adatok mennyiségét `readDf` . Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. `readDf`Hozzon létre egy ideiglenes táblát a dataframe használatával `temphvactable` . Ezután az ideiglenes táblázat segítségével hozzon létre egy kaptár-táblázatot `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Végül a struktúra táblázat segítségével hozzon létre egy táblát az adatbázisban. A következő kódrészlet a `hvactable` Azure SQL Databaseban jön létre.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Kapcsolódjon a Azure SQL Database a SSMS használatával, és ellenőrizze, hogy látható- `dbo.hvactable` e.

    a. Indítsa el a SSMS, és kapcsolódjon a Azure SQL Databasehoz a kapcsolat részleteinek megadásával, ahogy az alábbi képernyőképen is látható.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png " alt-text="Kapcsolódás SQL Database a SSMS1 használatával" border="true":::

    b. **Object Explorerból** bontsa ki az adatbázist és a tábla csomópontot, hogy megtekintse a **dbo. hvactable** létrejöttét.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png " alt-text="Kapcsolódás SQL Database a SSMS2 használatával" border="true":::

1. Futtasson egy lekérdezést a SSMS-ben a tábla oszlopainak megtekintéséhez.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Adatfolyam-továbbítás Azure SQL Databaseba

Ebben a szakaszban az előző szakaszban létrehozott adatátviteli folyamatokat továbbítjuk `hvactable` .

1. Első lépésként győződjön meg arról, hogy nincsenek rekordok a-ben `hvactable` . A SSMS használatával futtassa a következő lekérdezést a táblán.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Hozzon létre egy új Jupyter Notebook a HDInsight Spark-fürtön. Illessze be a következő kódrészletet a kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Az adatok továbbítása a **HVAC.csv** a rendszerbe `hvactable` . HVAC.csv fájl elérhető a fürtben a következő helyen: `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . A következő kódrészletben először megkapjuk a továbbított adatmennyiség sémáját. Ezután létrehozunk egy streaming dataframe az adott séma használatával. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. A kimenet a **HVAC.csv** sémáját jeleníti meg. A `hvactable` ugyanazzal a sémával is rendelkezik. A kimenet listázza a tábla oszlopait.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png " alt-text="&quot;hdinsight Apache Spark séma tábla&quot;" border="true":::

1. Végül használja az alábbi kódrészletet az adatok beolvasásához a HVAC.csvból, és továbbítsa azt a `hvactable` -adatbázisba. Illessze be a kódrészletet egy kód cellájába, cserélje le a helyőrző értékeket az adatbázis értékeire, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. A következő lekérdezés futtatásával ellenőrizze, hogy az adattovábbítás folyamatban van-e a `hvactable` SQL Server Management Studioban (SSMS). A lekérdezés minden futtatásakor a tábla sorainak száma növekszik.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Következő lépések

* [Az HDInsight Spark-fürt használata Data Lake Storageban lévő adatelemzéshez](apache-spark-use-with-data-lake-store.md)
* [Adatterhelés és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsight](apache-spark-load-data-run-query.md)
* [Apache Spark strukturált streaming használata a HDInsight Apache Kafka használatával](../hdinsight-apache-kafka-spark-structured-streaming.md)
