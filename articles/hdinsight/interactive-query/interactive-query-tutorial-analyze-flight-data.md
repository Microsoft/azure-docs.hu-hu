---
title: 'Oktatóanyag: ETL-műveletek interaktív lekérdezéssel – Azure HDInsight'
description: Oktatóanyag – megtudhatja, hogyan nyerheti ki az adatokat nyers CSV-adatkészletből. Alakítsa át az interaktív lekérdezés használatával a HDInsight. Ezután az Apache Sqoop használatával töltse be az átalakított adatkészleteket az Azure SQL Database-be.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 7413a32fdddb579bad61c9cfe539be6aaeae9881
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81313739"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezés használatával

Ebben az oktatóanyagban egy nyers CSV-adatfájlt tölt le nyilvánosan elérhető repülési adatfájlból. Importálja a HDInsight-fürt tárterületére, majd az Azure HDInsight interaktív lekérdezésével alakítsa át az adataikat. Az adatátalakítást követően a rendszer az [Apache Sqoop](https://sqoop.apache.org/)használatával tölti be ezeket az Azure SQL Database-adatbázisba.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A repülőjárat-mintaadatok letöltése
> * Az adatok feltöltése egy HDInsight-fürtre
> * Az adatátalakítás interaktív lekérdezés használatával
> * Tábla létrehozása az Azure SQL Database-ben
> * Az Sqoop használata az Azure SQL Database-adatbázisba való exportáláshoz

## <a name="prerequisites"></a>Előfeltételek

* Egy interaktív lekérdezési fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza az **interaktív lekérdezés** a **fürt típusához**lehetőséget.

* Egy Azure SQL Database. Egy Azure SQL-adatbázist használ céladattárként. Ha még nem rendelkezik SQL-adatbázissal, olvassa el az [Azure SQL-adatbázis az Azure Portalon történő létrehozását](/azure/sql-database/sql-database-single-database-get-started) ismertető cikket.

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>A repülőjárat-adatok letöltése

1. Nyissa meg a [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Kutatási és Innovációs Műszaki Felügyelőség, Közlekedési Statisztikai Hivatal) oldalt.

2. A lapon törölje az összes mezőt, majd válassza ki a következő értékeket:

   | Name (Név) | Érték |
   | --- | --- |
   | Filter Year (Szűrési év) |2019 |
   | Filter Period (Szűrési időszak) |January |
   | Mezők |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Válassza a **Download** (Letöltés) lehetőséget. Egy .zip fájlt kap, amely a kiválasztott adatmezőket tartalmazza.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Az adatok feltöltése egy HDInsight-fürtre

Számos különböző módon tölthet fel adatokat egy HDInsight-fürthöz tartozó tárolóra. Ebben a szakaszban az `scp` segítségével fogja feltölteni az adatokat. Az adatok feltöltésének egyéb módjaival kapcsolatban lásd: [Adatok feltöltése a HDInsightba](../hdinsight-upload-data.md).

1. Töltse fel a. zip fájlt a HDInsight-fürt fő csomópontjára. Szerkessze az alábbi parancsot úgy `FILENAME` , hogy a. zip-fájl nevét és a `CLUSTERNAME` HDInsight-fürt nevét helyettesíti. Ezután nyisson meg egy parancssort, állítsa be a munkakönyvtárat a fájl helyére, majd írja be a parancsot.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Ha a rendszer kéri, adja meg az igen vagy a nem értéket a folytatáshoz. A beírt szöveg nem látható az ablakban.

2. Ha a feltöltés befejeződött, csatlakozzon a fürthöz az SSH-val. Szerkessze az alábbi parancsot úgy `CLUSTERNAME` , hogy lecseréli a nevet a HDInsight-fürt nevére. Ezután írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Környezeti változó beállítása az SSH-kapcsolatok létrehozása után. `SQL_SERVERNAME`Cserélje `FILE_NAME`le a `SQL_DATABASE`, `SQL_USER`,, `SQL_PASWORD` , és értéket a megfelelő értékekre. Ezután írja be a parancsot:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Bontsa ki a. zip fájlt az alábbi parancs beírásával:

    ```bash
    unzip $FILENAME.zip
    ```

5. Hozzon létre egy könyvtárat a HDInsight-tárolóban, majd másolja a. csv fájlt a könyvtárba az alábbi parancs beírásával:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Adatok átalakítása egy Hive-lekérdezéssel

Számos módon futtathat Hive-feladatokat egy HDInsight-fürtön. Ebben a szakaszban a [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) használatával futtat egy kaptár-feladatot. A kaptár-feladatok futtatásának egyéb módjaival kapcsolatos információkért lásd: [Apache Hive használata a HDInsight-on](../hadoop/hdinsight-use-hive.md).

A Hive-feladat keretében importálja az adatokat a .csv fájlból egy **Delays** (Késések) nevű Hive-táblába.

1. A HDInsight-fürthöz már megjelenő SSH-parancssorból használja az alábbi parancsot a **flightdelays. HQL**nevű új fájl létrehozásához és szerkesztéséhez:

    ```bash
    nano flightdelays.hql
    ```

2. A fájl tartalma legyen a következő szöveg:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. A fájl mentéséhez nyomja le a **CTRL + X**billentyűkombinációt, majd az **y**billentyűt, majd írja be a következőt:.

4. Indítsa el a Hive-ot, és futtassa a **flightdelays.hql** fájlt az alábbi paranccsal:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Miután a **flightdelays.hql** szkript lefutott, a következő paranccsal nyisson meg egy interaktív Beeline-munkamenetet:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Amikor a `jdbc:hive2://localhost:10001/>` parancssor megjelenik, a következő lekérdezéssel nyerhet ki adatokat az importált repülőjárat-késési adatokból:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Ez a lekérdezés lekéri azon városok listáját, ahol időjárás miatti késések történtek, valamint a késések átlagos idejét, és menti ezeket az adatokat a következő helyen: `/tutorials/flightdelays/output`. Később a Sqoop erről a helyről olvassa be az adatokat, amelyeket exportál az Azure SQL Database-be.

7. A Beeline-ból való kilépéshez írja be a parancssorba a `!quit` parancsot.

## <a name="create-a-sql-database-table"></a>SQL Database-tábla létrehozása

Számos módon csatlakozhat az SQL Database-hez, majd hozhat létre egy táblát. A következő lépések során a [FreeTDS](http://www.freetds.org/) eszközt használjuk a HDInsight-fürtről.

1. A FreeTDS telepítéséhez használja a következő parancsot az Open SSH-kapcsolatban a fürthöz:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. A telepítés végeztével futtassa a következő parancsot az SQL-adatbáziskiszolgálóhoz való csatlakozáshoz.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    A kimenet a következő szöveghez fog hasonlítani:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Az `1>` parancssorban írja be a következő sorokat:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    A `GO` utasítás megadásakor a rendszer kiértékeli az előző utasításokat. Ez az utasítás egy **késések**nevű táblát hoz létre fürtözött indexszel.

    Az alábbi lekérdezéssel ellenőrizheti, hogy a tábla létrejött-e:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    A kimenet az alábbi szöveghez hasonló:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. A tsql eszközből való kilépéshez írja be az `exit` kifejezést az `1>` parancssorba.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Az SQL Database-be való exportálás az Apache Sqoop használatával

Az előző szakaszok során átmásolta az átalakított adatokat a következő helyre: `/tutorials/flightdelays/output`. Ebben a szakaszban a Sqoop segítségével fogja exportálni az adatokat az `/tutorials/flightdelays/output` helyről az Azure SQL-adatbázisban létrehozott táblába.

1. Az alábbi parancs beírásával ellenőrizze, hogy a Sqoop látható-e az SQL Database-ben:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Ez a parancs az adatbázisok listáját adja vissza, beleértve azt az adatbázist, amelyben korábban `delays` létrehozta a táblát.

2. Exportálja az `/tutorials/flightdelays/output` adatokat a `delays` táblázatba az alábbi parancs beírásával:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    A Sqoop a `delays` táblázatot tartalmazó adatbázishoz csatlakozik, és a `/tutorials/flightdelays/output` címtárból exportálja az adatait a `delays` táblába.

3. A sqoop parancs befejeződése után a TSQL segédprogrammal csatlakozhat az adatbázishoz az alábbi parancs beírásával:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    A következő utasításokkal ellenőrizheti, hogy az adatok exportálva lettek-e a delays táblába:

    ```sql
    SELECT * FROM delays
    GO
    ```

    A táblában látnia kell az adatok listáját. A tábla a városok nevét és az egyes városokhoz tartozó átlagos késések idejét tartalmazza.

    A tsql eszközből való kilépéshez írja be az `exit` parancsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy nyers CSV-adatfájlt vett fel, egy HDInsight-fürtbe importálta, majd az Azure HDInsight interaktív lekérdezés használatával alakította át az adatelemzést.  Folytassa a következő oktatóanyaggal, amely a Apache Hive Warehouse-összekötő megismerését ismerteti.

> [!div class="nextstepaction"]
> [Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel](./apache-hive-warehouse-connector.md)
