---
title: Hadoop-fürtben lévő adatelemzési folyamat vizsgálata
description: A csoportos adatelemzési folyamat egy végpontok közötti forgatókönyv esetén, amely egy HDInsight Hadoop-fürtöt használ egy modell létrehozásához és üzembe helyezéséhez.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53f50e98bcec4b8ace342808f0bcfd96770834b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002221"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>A csoportos adatelemzési folyamat működés közben: Azure HDInsight Hadoop fürtök használata
Ebben az útmutatóban egy végpontok közötti forgatókönyvben használjuk a [csoportos adatelemzési folyamatot (TDSP)](overview.md) . Egy Azure HDInsight Hadoop- [fürtöt](https://azure.microsoft.com/services/hdinsight/) használunk a nyilvánosan elérhető [NYC-taxis](https://www.andresmh.com/nyctaxitrips/) adatkészletből származó adatok tárolására, megismerésére és szolgáltatására, valamint az adatok leállására. A bináris és a többosztályos besorolás és a regressziós prediktív feladatok kezeléséhez az adatok modelljeit Azure Machine Learning használatával kell kiépíteni. 

A nagyobb adatkészletek kezelésére szolgáló bemutatóhoz lásd: [csoportos adatelemzési folyamat – Azure HDInsight Hadoop fürtök használata 1 TB-os adatkészleten](hive-criteo-walkthrough.md).

Az 1 TB-os adatkészletet használó bemutatóban bemutatott feladatok elvégzéséhez IPython notebookot is használhat. További információ: Criteo- [útmutató a kaptár ODBC-kapcsolatok használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC taxi TRIPS adatkészlet leírása
A New York-i taxi Trip-adat körülbelül 20 GB tömörített, vesszővel tagolt (CSV) fájlból áll (a ~ 48 GB tömörítetlen). Több mint 173 000 000 egyedi utazást tartalmaz, és tartalmazza az egyes utazásokhoz fizetett viteldíjat. Az egyes utazási rekordok tartalmazzák a pick-up és a lemorzsolódási helyét és időpontját, a névtelen hack-(illesztőprogram-) licenc számát és a Digitális medál-számot (a taxi egyedi AZONOSÍTÓját). Az adat a 2013-as év összes utazására vonatkozik, és a következő két adatkészletben szerepel minden hónapban:

- A trip_data CSV-fájlok az utazás részleteit tartalmazzák: az utasok számát, a beléptetést és a lemorzsolódási, az utazási időtartamot és a menetidő hosszát. Íme néhány példa a rekordokra:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- Az trip_fare CSV-fájlok tartalmazzák az egyes utazások díjait, a fizetés típusát, a díjszabást, a pótdíjat, az adókat, a tippeket és az autópályadíjat, valamint a teljes fizetett összeget. Íme néhány példa a rekordokra:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Az utazáshoz és az utazáshoz való csatlakozáshoz szükséges egyedi kulcs \_ \_ a következő mezőkből áll: emlékérem, Hack \_ License és a pickup \_ DateTime. Egy adott útra vonatkozó összes adat beszerzéséhez elegendő a három kulcshoz csatlakozni.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Példák az előrejelzési feladatokra
Határozza meg, hogy milyen előrejelzések alapján kívánja végrehajtani az adatelemzést a szükséges folyamat-feladatok tisztázásához. Íme három példa az útmutatóban ismertetett előrejelzési problémákra, mindezt a tip- *\_ összeg* alapján:

- **Bináris besorolás**: megjósolhatja, hogy fizetett-e borravalót egy útra. Ez azt a *tippet \_* jelöli, amely nagyobb, mint $0 pozitív példa, míg a $0-es számú *Tipp \_* negatív példa.

  - 0. osztály: tip_amount = $0
  - 1. osztály: tip_amount > $0

- **Többosztályos besorolás**: az útra kifizetett tip-összegek tartományának előrejelzése. A *Tipp \_ összegét* öt osztályra osztjuk:

  - 0. osztály: tip_amount = $0
  - 1. osztály: tip_amount > $0 és tip_amount <= $5
  - 2. osztály: tip_amount > $5 és tip_amount <= $10
  - 3. osztály: tip_amount > $10 és tip_amount <= $20
  - 4. osztály: tip_amount > $20

- **Regressziós feladat**: előre megjósolható, hogy a tipp mennyi összeget fizetett ki egy útra.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>HDInsight Hadoop-fürt beállítása a speciális elemzésekhez
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

Beállíthat egy Azure-környezetet a speciális elemzésekhez, amelyek a HDInsight-fürtöt három lépésben alkalmazzák:

1. [Storage-fiók létrehozása](../../storage/common/storage-account-create.md): ezt a Storage-fiókot használjuk az Azure Blob Storage-ban tárolt adattároláshoz. A HDInsight-fürtökben használt adat itt is található.
2. [Testreszabhatja Azure HDInsight Hadoop fürtöket a fejlett elemzési folyamathoz és technológiához](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ez a lépés létrehoz egy HDInsight Hadoop-fürtöt, amely az összes csomóponton telepítve van a 64 bites anaconda Python 2,7. A HDInsight-fürt testreszabása során két fontos lépést kell megjegyeznünk.
   
   * Ne felejtse el összekapcsolni az 1. lépésben létrehozott Storage-fiókot a HDInsight-fürttel a létrehozásakor. Ez a Storage-fiók fér hozzá a fürtön belül feldolgozott adatszolgáltatásokhoz.
   * A fürt létrehozása után engedélyezze a távoli hozzáférést a fürt fő csomópontjához. Keresse meg a **konfiguráció** lapot, és válassza a **távoli engedélyezése** lehetőséget. Ez a lépés a távoli bejelentkezéshez használt felhasználói hitelesítő adatokat határozza meg.
3. [Azure Machine learning munkaterület létrehozása](../classic/create-workspace.md): ezt a munkaterületet gépi tanulási modellek létrehozásához használhatja. Ez a feladat a kezdeti adatfeltárás és-mintavételezés befejezése után, a HDInsight-fürt használatával foglalkozik.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Adatok beolvasása nyilvános forrásból
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

Ha a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészletet a saját gépén szeretné átmásolni a gépre, használja az [adatok áthelyezése az Azure Blob Storage-ba és az-ból](move-azure-blob.md)című témakörben leírt módszerek egyikét.

Itt azt ismertetjük, hogyan lehet a AzCopy használatával átvinni az adatokból származó fájlokat. A AzCopy letöltéséhez és telepítéséhez kövesse az [első lépések a AzCopy parancssori segédprogrammal](../../storage/common/storage-use-azcopy-v10.md)című témakör útmutatását.

1. A parancssori ablakban futtassa a következő AzCopy-parancsokat, és cserélje le a elemet *\<path_to_data_folder>* a kívánt célhelyre:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. A másolás befejezésekor a kiválasztott adatmappa összesen 24 tömörített fájlt fog látni. Bontsa ki a letöltött fájlokat a helyi gépen található könyvtárba. Jegyezze fel azt a mappát, ahol a tömörítetlen fájlok találhatók. Ezt a mappát nevezzük a *\<path\_to\_unzipped_data\_files\>* következőnek:.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Az adatok feltöltése a HDInsight Hadoop-fürt alapértelmezett tárolójába
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

A következő AzCopy-parancsokban cserélje le a következő paramétereket a Hadoop-fürt létrehozásakor megadott tényleges értékekre, és adja meg az adatfájlok kicsomagolását.

* ***\<path_to_data_folder>*** A kibontott adatfájlokat tartalmazó könyvtár (az elérési úttal együtt) a gépen.  
* ***\<storage account name of Hadoop cluster>*** A HDInsight-fürthöz társított Storage-fiók.
* ***\<default container of Hadoop cluster>*** A fürt által használt alapértelmezett tároló. Az alapértelmezett tároló neve általában megegyezik a fürt nevével. Ha például a fürt neve "abc123.azurehdinsight.net", az alapértelmezett tároló a abc123.
* ***\<storage account key>*** A fürt által használt Storage-fiók kulcsa.

A parancssorból vagy egy Windows PowerShell-ablakból futtassa a következő két AzCopy parancsot.

Ezzel a paranccsal feltöltheti az utazási adatok a ***nyctaxitripraw*** könyvtárba a Hadoop-fürt alapértelmezett tárolójában.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
```

Ezzel a paranccsal feltöltheti a díjszabási adatok a ***nyctaxifareraw*** könyvtárba a Hadoop-fürt alapértelmezett tárolójában.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv
```

Az adatmennyiségnek most a blob Storage-ban kell lennie, és készen kell állnia a HDInsight-fürtön belüli felhasználásra.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Jelentkezzen be a Hadoop-fürt fő csomópontjára, és készüljön fel a felderítő adatelemzésre
> [!NOTE]
> Ez általában egy rendszergazdai feladat.
> 
> 

A fürt fő csomópontjának a felderítő adatelemzéshez és az adat-leállási mintavételezéshez való hozzáféréséhez kövesse az [Hadoop-fürt fő csomópontjának eléréséhez](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakörben leírt eljárást.

Ebben az útmutatóban elsősorban a [kaptárban](https://hive.apache.org/), egy SQL-szerű lekérdezési nyelven írt lekérdezéseket használjuk az előzetes adatfeltárások végrehajtásához. A kaptár-lekérdezések a ". HQL" fájlban tárolódnak. Ezután leállítjuk azokat az adattípusokat, amelyeket a modellek létrehozásához Machine Learning használhat.

Ha a fürtöt a felderítő adatok elemzéséhez szeretné előkészíteni, töltse le a megfelelő kaptár-parancsfájlokat tartalmazó ". HQL" fájlokat a [githubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) egy helyi könyvtárba (C:\Temp) a fő csomóponton. Nyissa meg a parancssort a fürt fő csomópontján belül, és futtassa a következő két parancsot:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

Ez a két parancs letölti az ebben a bemutatóban szükséges összes ". HQL" fájlt a C:\Temp helyi könyvtárának ***&#92;*** .

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Struktúra-adatbázis és-táblázatok létrehozása hónap szerint particionálva
> [!NOTE]
> Ez a feladat általában rendszergazdák számára készült.
> 
> 

Most már készen áll a New York-i taxi-adatkészlethez tartozó kaptár-táblázatok létrehozására.
A Hadoop-fürt fő csomópontján nyissa meg a Hadoop parancssort a fő csomópont asztalán. Adja meg a kaptár könyvtárat a következő parancs futtatásával:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Futtassa az ebben a bemutatóban található összes struktúra-parancsot a kaptár bin/Directory parancssorból. Ez automatikusan kezeli az elérési utakkal kapcsolatos problémákat. Ebben az útmutatóban a "kaptár könyvtár promptja", a "kaptárak/könyvtár promptja" és a "Hadoop parancssor" kifejezést használjuk.
> 
> 

A kaptár-könyvtár parancssorában futtassa a következő parancsot a kaptár-adatbázist és-táblákat létrehozó fő csomópont Hadoop parancssorában:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Itt látható a **C:\Temp\sample \_ struktúra \_ létrehozása \_ db \_ és \_ Tables. HQL** fájl, amely létrehozza a kaptár-adatbázis **nyctaxidb**, valamint **a táblákat** és a **viteldíjat**.

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

Ez a kaptár-parancsfájl két táblát hoz létre:

* Az **utazási** táblázat az egyes Ride-túrák részleteit (az illesztőprogram részleteit, a kivételezési időt, az utazási távolságot és az időpontokat) tartalmazza.
* A **díjszabási** táblázat tartalmazza a viteldíjak részleteit (viteldíj összege, Tipp összege, autópályadíj és pótdíj).

Ha további segítségre van szüksége ezekkel az eljárásokkal kapcsolatban, vagy alternatívát szeretne kivizsgálni, tekintse meg a [kaptár-lekérdezések elküldése közvetlenül a Hadoop parancssorból](move-hive-tables.md#submit)című szakaszt.

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Adatgyűjtés a struktúra tábláiba partíciók szerint
> [!NOTE]
> Ez a feladat általában rendszergazdák számára készült.
> 
> 

A New York-i taxi-adatkészlet egy hónap alatt természetes particionálással rendelkezik, amelyet a gyorsabb feldolgozás és a lekérdezési idő érdekében használunk. A következő PowerShell-parancsok (a kaptár-könyvtárból a Hadoop parancssor használatával) betöltik az adatait az utazás és a viteldíj-struktúra tábláiba, havonta particionálva.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

A **minta \_ struktúrában a \_ \_ \_ \_ Partitions. HQL** fájl a következő **betöltési** parancsokat tartalmazza:

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

A feltárási folyamat során használt kaptár-lekérdezések száma csak egy vagy két partíciót érint. Ezek a lekérdezések azonban a teljes adathalmazon is futtathatók.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Adatbázisok megjelenítése a HDInsight Hadoop-fürtben
A HDInsight Hadoop-fürtben létrehozott adatbázisok megjelenítéséhez a Hadoop parancssori ablakban futtassa a következő parancsot a Hadoop parancssorban:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>A **nyctaxidb** -adatbázisban lévő kaptár táblák megjelenítése
A **nyctaxidb** -adatbázisban lévő táblák megjelenítéséhez futtassa a következő parancsot a Hadoop parancssorban:

```console
hive -e "show tables in nyctaxidb;"
```

A következő parancs futtatásával ellenőrizheti, hogy a táblák particionálva vannak-e:

```console
hive -e "show partitions nyctaxidb.trip;"
```

Itt látható a várt kimenet:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

Hasonlóképpen, a következő parancs futtatásával biztosíthatja, hogy a díjszabási tábla particionálva legyen:

```console
hive -e "show partitions nyctaxidb.fare;"
```

Itt látható a várt kimenet:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Adatfelderítési és-funkciós mérnöki struktúra
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

A kaptár-lekérdezések segítségével adatfeltárási és-szolgáltatás-tervezési feladatokat hajthat végre a struktúra tábláiba betöltött adatokhoz. Íme néhány példa ilyen feladatokra:

* A két tábla első 10 rekordjának megtekintése.
* Ismerkedjen meg néhány mező adateloszlásával a különböző időtartományokban.
* Vizsgálja meg a hosszúsági és a szélességi mezők adatminőségét.
* Bináris és többosztályos besorolási Címkék készítése a tip-összeg alapján.
* Szolgáltatások előállítása a közvetlen utazási távolságok kiszámításával.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Feltárás: a Table Trip első 10 rekordjának megtekintése
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

Ha szeretné megtekinteni az adatok megjelenését, vizsgálja meg az egyes táblák 10 rekordját. A rekordok vizsgálatához futtassa a következő két lekérdezést a Hadoop parancssori konzolon, a kaptár könyvtárának parancssorában.

A legjobb 10 rekord beszerzése az első hónapból a Trip táblában:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

A legjobb 10 rekord beszerzése a viteldíj táblában az első hónapból:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

Mentheti a rekordokat egy fájlba, hogy az előző lekérdezés kis módosításával kényelmesen megtekinthető legyen:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Feltárás: megtekintheti a rekordok számát a 12 partícióban
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

Az érdekesség az, hogy az utazások száma milyen mértékben változik a naptári év során. A hónap szerinti csoportosítás az utak eloszlását mutatja.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

Ez a parancs a következő kimenetet hozza létre:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

Itt az első oszlop a hónap, a második pedig az adott hónaphoz tartozó utak száma.

A következő parancsnak a kaptár könyvtárában való futtatásával is megszámolhatja az utazás adatkészletében lévő rekordok teljes számát:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

Ez a parancs a következőket eredményezi:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Az utazás adatkészletéhez hasonló parancsok használatával kiállíthat kaptár-lekérdezéseket a díjszabási adatkészlet számára a rekordok számának ellenőrzéséhez.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

Ez a parancs a következő kimenetet hozza létre:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

A havonta megegyező számú utazást mindkét adatkészletnél visszaadja a rendszer, így biztosítva az első érvényesítést, hogy az adatokat helyesen töltötték be.

A viteldíj-adatkészletben lévő rekordok teljes számát a következő parancs alapján számíthatja ki a kaptár könyvtárának parancssorában:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

Ez a parancs a következőket eredményezi:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

A két táblában található rekordok teljes száma szintén azonos, és egy második érvényesítést biztosít az adatok megfelelő betöltéséhez.

### <a name="exploration-trip-distribution-by-medallion"></a>Kutatás: utazások eloszlása a medál alapján
> [!NOTE]
> Ez az elemzés általában egy adattudós-feladat.
> 
> 

Ez a példa az adott időszakon belül 100-nál nagyobb számú medált (taxi számot) azonosít. A lekérdezés a particionált tábla-hozzáférés előnyeit élvezheti, mert a partíció változó **hónapja** feltétele. A lekérdezés eredményei egy helyi fájlba, a **queryoutput. TSV** fájlba íródnak, a `C:\temp` fő csomóponton.

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Itt látható a **minta \_ kaptár- \_ utazások \_ számának tartalma a \_ \_ medalion. HQL** -fájl alapján a vizsgálathoz.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

A New York-i taxi adatkészlete egy egyedi cab-t azonosít. Megadhatja, hogy mely megfelelőségértékelő központok legyenek viszonylag elfoglalva azzal, hogy egy adott időszakon belül egy adott számú utazáson több útra is tettek. Az alábbi példa azokat a megfelelőségértékelő-ket azonosítja, amelyek az első három hónapban több mint száz utazást hajtottak végre, és a lekérdezési eredményeket egy helyi fájlba, a **C:\temp\queryoutput.TSV** menti.

Itt látható a **minta \_ kaptár- \_ utazások \_ számának tartalma a \_ \_ medalion. HQL** -fájl alapján a vizsgálathoz.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Futtassa a következő parancsot a kaptár könyvtárában:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Feltárás: az utazások terjesztése a medál és a hack licenc alapján
> [!NOTE]
> Ez a feladat általában egy adattudós számára készült.
> 
> 

Az adathalmazok feltárásakor gyakran szeretnénk megvizsgálni az értékek csoportjai eloszlását. Ez a szakasz bemutatja, hogyan végezheti el ezt az elemzést a vezetőfülkék és az illesztőprogramok számára.

A **minta \_ kaptár- \_ utazások \_ száma a medál- \_ \_ \_ licenc. HQL** -fájl csoportosítja a díjszabási adatkészletet a **medál** és a **hack_license** között, és az egyes kombinációk számát adja vissza. A tartalma:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

Ez a lekérdezés a vezetőfülke és az illesztőprogram kombinációját adja vissza, amely csökkenő számú TRIPS szerint rendezve van.

A kaptár könyvtárának parancssorában futtassa a következőt:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

A lekérdezés eredményei egy helyi fájlba íródnak, a **C:\temp\queryoutput.TSV**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Feltárás: az adatminőség felmérése érvénytelen hosszúsági vagy szélességi rekordok ellenőrzésével
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

A feltáró adatok elemzésének közös célja, hogy az érvénytelen vagy hibás rekordok kiszűrése megtörténjen. Az ebben a szakaszban szereplő példa azt határozza meg, hogy a hosszúsági vagy szélességi mezők a New York-i területtől távol eső értéket tartalmaznak-e. Mivel valószínű, hogy az ilyen rekordok hibás földrajzi szélességi értékkel rendelkeznek, a modellezéshez használni kívánt adatokból szeretnénk megtörölni azokat.

Itt látható a **minta \_ struktúra \_ minőségi \_ Assessment. HQL** -fájljának tartalma a vizsgálathoz.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

A kaptár könyvtárának parancssorában futtassa a következőt:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

A parancsban szereplő *-S* argumentum letiltja a struktúra-hozzárendelési Térkép/feladatok csökkentése állapot képernyőjét. Ez a parancs azért hasznos, mert a kaptár-lekérdezés kimenete olvashatóbb formában jelenik meg.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Feltárás: az utazási tippek bináris osztályának eloszlása
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

Az [előrejelzési feladatok](hive-walkthrough.md#mltasks) című szakaszban ismertetett bináris besorolási probléma esetén hasznos lehet megállapítani, hogy van-e megadva tipp. A tippek ezen eloszlása bináris:

* Tipp megadva (1. osztály, Tipp \_ összege > $0)  
* nincs tipp (0. osztály, Tipp \_ mennyisége = $0)

A következő minta kimutatott **\_ \_ \_ frekvenciákon. HQL** fájl a futtatandó parancsot mutatja:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

A kaptár könyvtárának parancssorában futtassa a következőt:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Feltárás: osztályok eloszlása a többosztályos beállításban
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

Az [előrejelzési feladatok](hive-walkthrough.md#mltasks) című szakaszban ismertetett többosztályos besorolási probléma esetén ez az adatkészlet természetes besorolást is biztosít, hogy előre megjósolja a megadott tippek mennyiségét. A lekérdezésben használhatunk raktárhelyeket a tip-tartományok definiálásához. A különböző tip-tartományok osztály-eloszlásának beszerzéséhez használja a **minta \_ kaptár \_ tip \_ Range \_ frekvenciákon. HQL** fájlt. Itt látható a tartalma.

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Futtassa a következő parancsot a Hadoop parancssori konzolról:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Feltárás: a közvetlen távolság kiszámításának két földrajzi hosszúság – szélesség között
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

Előfordulhat, hogy tudni szeretné, hogy van-e különbség a két helyszín közötti közvetlen távolság és a taxi tényleges távolsága között. Előfordulhat, hogy az utas kevésbé valószínű, ha kitalálják, hogy az illesztőprogram egy hosszabb útvonalon szándékosan lett elvégezve.

A tényleges utazási távolság és a [Haversine közötti távolság](https://en.wikipedia.org/wiki/Haversine_formula) (a "nagy kör" távolság) közötti különbség megtekintéséhez használhatja a kaptáron belül elérhető trigonometriai függvényeket:

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

Az előző lekérdezésben az R a föld sugarát mérföldben, a PI pedig radián-re lesz konvertálva. A földrajzi hosszúság – szélesség pontok úgy vannak szűrve, hogy eltávolítsanak a New York-i régiótól távol lévő értékeket.

Ebben az esetben az eredményeket egy **queryoutputdir** nevű könyvtárba írjuk. A következő parancsok sora először létrehozza ezt a kimeneti könyvtárat, majd futtatja a kaptár parancsot.

A kaptár könyvtárának parancssorában futtassa a következőt:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

A lekérdezés eredményét kilenc Azure-blobra (**queryoutputdir/000000 kódot \_ 0** –  **queryoutputdir/000008 \_ 0**), a Hadoop-fürt alapértelmezett tárolójában kell írni.

Az egyes Blobok méretének megtekintéséhez futtassa a következő parancsot a kaptár könyvtárának parancssorában:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Ha szeretné megtekinteni egy adott fájl tartalmát, mondjuk a **000000 kódot \_ 0**, használja a Hadoop `copyToLocal` parancsot.

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> `copyToLocal` nagy méretű fájlok esetében nagyon lassú lehet, és nem ajánlott velük használni.  
> 
> 

Ennek az adatnak az egyik fő előnye az, hogy az [adatimportálási][import-data] modul használatával megvizsgáljuk Machine Learningon belüli adataikat.

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Leállt – mintaadatok és modellek készítése Machine Learning
> [!NOTE]
> Ez általában egy adattudós-feladat.
> 
> 

A feltáró adatelemzési fázis után most már készen áll arra, hogy felkészítsük a modelleket a Machine Learningban. Ebben a szakaszban bemutatjuk, hogyan használhatók a kaptár-lekérdezések az adatmintákhoz. Machine Learning ezután az [adatok importálása][import-data] modulból fér hozzá.

### <a name="down-sampling-the-data"></a>Lefelé – az adatmintavételezés
Ebben az eljárásban két lépés található. Először csatlakoztassa a **nyctaxidb. Trip** és a **nyctaxidb. fare** táblát az összes rekordban található három kulcshoz: **emlékérem**, **Hack \_ licenc**, és a **pickup \_ datetime**. Ezután előállítunk egy bináris besorolási **címkét, egy** kijelzett és egy többosztályos besorolási címkét, a **tip \_ osztályt**.

Ahhoz, hogy közvetlenül a Machine Learning [adatimportálási][import-data] modulból lehessen használni a lefelé vett mintákat, az előző lekérdezés eredményét egy belső struktúra-táblába kell menteni. A következő lépésekben létrehozunk egy belső struktúrás táblát, és feltöltjük annak tartalmát az összevont és a lefelé mintavételezéssel ellátott adatokkal.

A lekérdezés a standard kaptár-függvényeket közvetlenül alkalmazza a következő időpontok létrehozásához a **felvételi \_ dátum** /idő mezőben:
- nap órája
- év hete
- a hétköznap (1) a hétfő, a "7" pedig a vasárnapot jelenti.

A lekérdezés a közvetlen távolságot is létrehozza a kivételezési és a lemorzsolódási helyei között. Az ilyen függvények teljes listáját lásd: [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A lekérdezés ezután lekérdezi az adatmintákat, hogy a lekérdezés eredményei illeszkedjenek Azure Machine Learning Studio. A studióba csak az eredeti adatkészlet 1 százalékát importálja a rendszer.

Az alábbiakban látható a HQL-re vonatkozó, a **\_ \_ \_ \_ pénzmosás \_ teljes.** fájljának tartalma, amely előkészíti az adatmodell-létrehozási Machine learning:

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

A lekérdezés futtatása a kaptár könyvtárának parancssorából:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

Most már van egy belső tábla, **nyctaxidb.nyctaxi_downsampled_dataset**, amely a Machine learning [adatimportálási][import-data] moduljának használatával érhető el. Emellett ezt az adatkészletet használhatja Machine Learning modellek létrehozásához.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>A Machine Learning adatimportálási moduljának használata a lefelé mintavételezéssel ellátható adatértékek eléréséhez
A Machine Learning adatimportálási moduljában a kaptár-lekérdezések [kiküldéséhez][import-data] hozzáféréssel kell rendelkeznie egy Machine learning-munkaterülethez. Emellett hozzá kell férnie a fürt és a hozzá tartozó Storage-fiók hitelesítő adataihoz is.

Íme néhány információ az [adatimportálási][import-data] modulról és a bemeneti paraméterekről:

**HCatalog-kiszolgáló URI-ja**: Ha a fürt neve **abc123**, használja a következőket: https: \/ /abc123.azurehdinsight.net.

**Hadoop felhasználói fiók neve**: a fürthöz választott Felhasználónév (nem a távelérés felhasználóneve).

**Hadoop felhasználói fiók jelszava**: a fürthöz választott jelszó (nem a távelérési jelszó).

**Kimeneti adatokat tároló hely**: az Azure-ra lett kiválasztva.

**Azure Storage-fiók neve**: a fürthöz társított alapértelmezett Storage-fiók neve.

**Azure-tároló neve**: a fürt alapértelmezett tárolójának neve, és általában ugyanaz, mint a fürt neve. A **abc123** nevű fürt esetében a név a következő: abc123.

> [!IMPORTANT]
> Minden olyan tábla, amelyet a Machine Learning [adatimportálási][import-data] moduljának használatával szeretne lekérdezni, belső táblának kell lennie.
> 
> 

Itt megtudhatja, hogyan állapíthatja **meg, hogy** a **D. db** adatbázis egy belső tábla-e. Futtassa a következő parancsot a kaptár könyvtárában:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Ha a tábla egy belső tábla, és fel van töltve, a tartalma itt jelenik meg.

Egy másik módszer annak meghatározására, hogy a tábla belső tábla-e Azure Storage Explorer használata. Ezzel a beállítással megnyithatja a fürt alapértelmezett tárolójának nevét, majd szűrheti a tábla nevét. Ha a tábla és annak tartalma megjelenik, ez megerősíti, hogy egy belső tábla.

Itt látható a kaptár-lekérdezés és az [adatimportálási][import-data] modul képernyőképe:

![Képernyőfelvétel a kaptár-lekérdezésről az Adatimportálási modulhoz](./media/hive-walkthrough/1eTYf52.png)

Mivel a rendszer az alapértelmezett tárolóban tárolja a levett mintavételen alapuló adatforrásokat, a Machine Learningből származó kaptár-lekérdezés egyszerű. Ez csak egy **select * from nyctaxidb. nyctaxi \_ Downsampled- \_ adatok**.

Az adatkészlet mostantól kiindulási pontként használható Machine Learning modellek létrehozásához.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Modellek készítése Machine Learning
Most már folytathatja a [Machine learning](https://studio.azureml.net)-ben való üzembe helyezési és modell-telepítési modell kialakítását. Az adatgyűjtés készen áll arra, hogy felhasználhassa a korábban azonosított előrejelzési problémák kezelését:

- **Bináris besorolás**: megjósolhatja, hogy fizetett-e borravalót egy útra.

  **Használt tanuló:** Kétosztályos logisztikai regresszió

  a. Ehhez a problémához a cél (vagy osztály) **felirat van** kijelölve. Az eredeti, lefelé mintavételezés alatt álló adatkészlet néhány oszlopa van, amelyek célja a besorolási kísérlethez tartozó szivárgás. Különösen a **Tipp \_ osztály**, a **Tipp \_ mennyisége** és a **teljes \_ összeg** felfedi az adott címkével kapcsolatos információkat, amelyek teszteléskor nem érhetők el. Ezeket az oszlopokat az [adatkészletek kiválasztása][select-columns] modulban az oszlopok kijelölése elem használatával távolítjuk el.

  A következő ábra azt mutatja be a kísérletet, hogy egy adott utazásra fordítottak-e borravalót:

  ![A tipp kifizetésének előrejelzésére szolgáló kísérlet ábrája](./media/hive-walkthrough/QGxRz5A.png)

  b. Ebben a kísérletben a célzott címke eloszlása nagyjából 1:1 volt.

   A következő diagram a tipp osztály címkéjének eloszlását mutatja a bináris besorolási probléma esetén:

  ![Tip-osztályok feliratainak terjesztési diagramja](./media/hive-walkthrough/9mM4jlD.png)

    Ennek eredményeképpen a 0,987-es görbén (AUC) található terület beszerzése az alábbi ábrán látható módon:

  ![AUC-érték diagramja](./media/hive-walkthrough/8JDT0F8.png)

- **Többosztályos besorolás**: a korábban definiált osztályok használatával előre megjósolhatja az utazáshoz kifizetett borravalók tartományát.

  **Használt tanuló:** Többosztályos logisztikai regresszió

  a. Ennek a problémának a célja (vagy osztály) címkéje a **tip \_ osztály**, amely öt érték (0, 1, 2, 3, 4) egyikét veheti igénybe. Ahogy a bináris besorolás esetében is, van néhány oszlopunk, amely a kísérlethez célzott szivárgást céloz meg. Különösen a kitűzött, a **tip- \_ összeg** és a **teljes \_ összeg** felfedi az adott címkére vonatkozó **adatokat, amelyek** teszteléskor nem érhetők el. Ezeket az oszlopokat az [adatkészlet kijelölése oszlopban található oszlopok][select-columns] használatával távolítjuk el.

  Az alábbi ábrán azt a kísérletet láthatja, hogy a tipp melyik raktárhelyre várhatóan csökken. A Raktárhelyek: 0. osztály: tipp = $0, 1. osztály: tipp > $0 és tipp <= $5, 2. osztály: tipp > $5 és tipp <= $10, 3. osztály: tipp > $10 és tipp <= $20 és 4. osztály: tipp > $20.

  ![A tipphez tartozó bin előrejelzési kísérlet ábrája](./media/hive-walkthrough/5ztv0n0.png)

  Most megmutatjuk, hogy a tényleges tesztelési osztály hogyan néz ki. A 0. osztály és az 1. osztály is elterjedt, a többi osztály pedig ritka.

  ![A tesztelési osztály eloszlásának diagramja](./media/hive-walkthrough/Vy1FUKa.png)

  b. Ebben a kísérletben egy zavart mátrixot használunk az előrejelzési pontosság az itt látható módon:

  ![Keveredési mátrix](./media/hive-walkthrough/cxFmErM.png)

  Míg a pontosság osztály jó, a modell nem végez jó munkát a ritkább osztályokon a "learning".

- **Regressziós feladat**: az utazáshoz fizetett tipp mennyiségének előrejelzése.

  **Használt tanuló:** Megnövelt döntési fa

  a. Ehhez a problémához a cél (vagy osztály) címkéje a **tip- \_ mennyiség**. A cél szivárgások ebben az esetben a következők **:** kitűzött, **tip- \_ osztály** és **teljes \_ összeg**. Az összes ilyen változó felfedi a tip-mennyiségre vonatkozó információt, amely általában nem érhető el tesztelési időben. Ezeket az oszlopokat az [adatkészlet kijelölése oszlopban található oszlopok][select-columns] használatával távolítjuk el.

  Az alábbi ábrán az adott tipp mennyiségének előrejelzésére szolgáló kísérlet látható:

  ![A tipp számának előrejelzésére szolgáló kísérlet ábrája](./media/hive-walkthrough/11TZWgV.png)

  b. A regressziós problémák esetén mérjük meg az előrejelzés pontosság, az előrejelzésekben szereplő négyzetes hibának és a meghatározási együtthatónak megfelelően:

  ![Az előrejelzési statisztikák képernyőképe](./media/hive-walkthrough/Jat9mrz.png)

  Itt a meghatározás együtthatója 0,709, ami azt jelenti, hogy a modell együtthatói a variancia körülbelül 71 százalékát ismertetik.

> [!IMPORTANT]
> Ha többet szeretne megtudni a Machine Learningről, valamint arról, hogyan érheti el és használhatja azt, tekintse [meg a mi a Machine learning](../classic/index.yml). Emellett a [Azure AI Gallery](https://gallery.cortanaintelligence.com/) kísérletek széles skáláját fedi le, és alapos bevezetést biztosít a Machine learning képességeinek körébe.
> 
> 

## <a name="license-information"></a>Licencelési információk
Ezt a minta-bemutatót és a hozzá tartozó parancsfájlokat a Microsoft a MIT licenc alatt osztja meg. További információ: **LICENSE.txt** fájl a githubon található mintakód könyvtárában.

## <a name="references"></a>Hivatkozások
•    [Andrés MONROY NYC taxi TRIPS letöltési oldal](https://www.andresmh.com/nyctaxitrips/)  
•    [A New York-i taxis utazási adatvédelme Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• A    [New York-i taxi és a limuzin Bizottság kutatási és statisztikai adatai](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data