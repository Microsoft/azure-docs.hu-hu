---
title: Tömeges betöltés Apache Phoenix a psql használatával – Azure HDInsight
description: A psql eszközzel tömeges betöltési adatmennyiségeket tölthet be Apache Phoenix táblákba az Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d56c9b33d53be0e30769aef8934a2dbf43d06867
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944828"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Adatok tömeges betöltése az Apache Phoenixbe a psql használatával

A [Apache Phoenix](https://phoenix.apache.org/) egy [Apache HBase](../hbase/apache-hbase-overview.md)-ra épülő, nyílt forráskódú, nagymértékben párhuzamos, összehasonlítható, integrált adatbázis. A Phoenix SQL-szerű lekérdezéseket biztosít a HBase-en keresztül. A Phoenix a JDBC-illesztőprogramokat használva lehetővé teszi a felhasználók számára az SQL-táblák,-indexek,-nézetek és-folyamatok létrehozását, törlését és módosítását, valamint a upsert-sorok egyenkénti és tömeges megváltoztatását. A Phoenix noSQL natív fordítást használ ahelyett, hogy a MapReduce-t használja a lekérdezések fordításához, hogy alacsony késleltetésű alkalmazásokat hozzon létre a HBase-on. A Phoenix felhasználja az ügyfél által megadott kód futtatásának támogatását a kiszolgáló címterület részeként, és a kód végrehajtásával az adott helyen található. Ez lekicsinyíti az ügyfél/kiszolgáló adatátvitelt.  Ha az HDInsight-ben Phoenix használatával szeretne dolgozni az adataival, először hozzon létre táblákat, majd töltse be őket.

## <a name="bulk-loading-with-apache-phoenix"></a>Tömeges betöltés Apache Phoenix

Több módon is beszerezhetők az adatok a HBase-be, például az ügyféloldali API-k, a MapReduce-feladatok és a TableOutputFormat használatával, illetve az adatok manuális bevitele a HBase-rendszerhéj használatával. A Phoenix két módszert biztosít a CSV-táblázatok Phoenix-táblákba való betöltéséhez: egy ügyfél-betöltési eszközt `psql` és egy MapReduce-alapú tömeges betöltési eszközt.

Az `psql` eszköz egyszálas, és a legmegfelelőbb a megabájt vagy gigabájt adat betöltéséhez. A betöltendő CSV-fájloknak a ". csv" kiterjesztéssel kell rendelkezniük.  Az SQL-parancsfájlokat a parancssorban is megadhatja az `psql` ". SQL" fájlkiterjesztés használatával.

A tömeges betöltés a MapReduce használatával sokkal nagyobb adatmennyiségekhez használatos, jellemzően éles környezetben, mivel a MapReduce több szálat használ.

Az adattöltés megkezdése előtt ellenőrizze, hogy a Phoenix engedélyezve van-e, és hogy a lekérdezés időtúllépési beállításai a vártak.  Nyissa meg a HDInsight-fürt [Apache Ambari](https://ambari.apache.org/) irányítópultját, válassza a HBase, majd a konfiguráció lapot.  Görgessen lefelé, és ellenőrizze, hogy a Apache Phoenix a következőre van-e beállítva `enabled` :

![Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>`psql`Táblázatok tömeges betöltésére használható

1. Hozzon létre egy nevű fájlt `createCustomersTable.sql` , és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Hozzon létre egy nevű fájlt `listCustomers.sql` , és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```sql
    SELECT * from Customers;
    ```

1. Hozzon létre egy nevű fájlt `customers.csv` , és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Hozzon létre egy nevű fájlt `customers2.csv` , és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Nyisson meg egy parancssort, és módosítsa a könyvtárat az újonnan létrehozott fájlok helyére. A lenti CLUSTERNAME cserélje le a HBase-fürt tényleges nevére. Ezután hajtsa végre a kódot, hogy feltöltse a fájlokat a fürt átjárócsomóponthoz:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből módosítsa a könyvtárat a **psql** eszköz helyére. Hajtsa végre az alábbi parancsot:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Az adatmennyiség tömeges betöltése. Az alábbi kód a **Customers** táblát fogja létrehozni, majd feltölti az adatokkal.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    A `psql` művelet befejezését követően a következőhöz hasonló üzenetnek kell megjelennie:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Továbbra is használhatja az `psql` ügyfelek tábla tartalmának megtekintését. Hajtsa végre az alábbi kódot:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Azt is megteheti, hogy a [HBase Shell](./query-hbase-with-hbase-shell.md)vagy az [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) használatával kérdezi le az adatlekérdezéseket.

1. További adatok feltöltése. Most, hogy a tábla már létezik, a parancs megadja a táblát. Hajtsa végre az alábbi parancsot:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce használata tömeges betöltési táblákhoz

A fürtön keresztül terjesztett nagyobb átviteli sebességű betöltéshez használja a MapReduce Load eszközt. Ez a betöltő először átalakítja az összes adatmennyiséget a HFiles, majd a létrehozott HFiles a HBase.

1. Ez a szakasz az SSH-munkamenetet és a korábban létrehozott objektumokat is folytatja. A fenti lépésekkel hozza létre az **ügyfelek** táblát, és szükség szerint **customers.csv** fájlt. Szükség esetén hozza létre újra az SSH-kapcsolatot.

1. A **Customers** tábla tartalmának csonkítása. Az Open SSH-munkamenetből hajtsa végre az alábbi parancsokat:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Másolja a `customers.csv` fájlt a átjárócsomóponthoz az Azure Storage-ba.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Váltson a MapReduce tömeges betöltési parancs végrehajtási könyvtárába:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Indítsa el a CSV-MapReduce betöltőjét a (z) `hadoop` paranccsal a Phoenix Client jar használatával:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    A feltöltés befejeződése után a következőhöz hasonló üzenetnek kell megjelennie:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Ha a MapReduce-t a Azure Data Lake Storage használatával szeretné használni, keresse meg a Data Lake Storage gyökérkönyvtárat, amely a `hbase.rootdir` értéke `hbase-site.xml` . A következő parancsban a Data Lake Storage gyökérkönyvtára `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . Ebben a parancsban adja meg a bemeneti és kimeneti mappák Data Lake Storage paraméterként:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Az **psql** a korábban leírtak szerint lehet lekérdezni és megtekinteni. Használhatja a [HBase shellt](./query-hbase-with-hbase-shell.md)vagy az [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)-t is.

## <a name="recommendations"></a>Javaslatok

* Ugyanazt a tárolóeszközt használja a bemeneti és a kimeneti mappákhoz, vagy az Azure Storage (WASB) vagy a Azure Data Lake Storage (ADL) számára. Az adatok Azure Storage-ból Data Lake Storageba való átviteléhez a következő `distcp` parancsot használhatja:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Használjon nagyobb méretű munkavégző csomópontokat. A MapReduce tömeges másolásának leképezési folyamatai nagy mennyiségű ideiglenes kimenetet hoznak létre, amely kitölti az elérhető nem elosztott fájlrendszerbeli területet. Nagy mennyiségű tömeges betöltés esetén használjon több és nagyobb méretű munkavégző csomópontot. A fürthöz hozzárendelt munkavégző csomópontok száma közvetlenül befolyásolja a feldolgozási sebességet.

* A bemeneti fájlok felosztása ~ 10 GB-os adattömbökbe. A tömeges betöltés egy nagy tárolási igényű művelet, így a bemeneti fájlok több adattömbbe való felosztása jobb teljesítményt eredményez.

* Kerülje a régió-kiszolgálói hozzáférési pontok elérését. Ha a sor kulcsa monoton módon növekszik, a HBase szekvenciális írások a régió-kiszolgáló hotspotting okozhatják. A sor kulcsának *sói* csökkentik a szekvenciális írásokat. A Phoenix lehetővé teszi, hogy a sor kulcsát transzparens módon sózzuk egy adott tábla egy adott táblára vonatkozóan, az alábbi hivatkozással.

## <a name="next-steps"></a>Következő lépések

* [Tömeges betöltés Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Apache Phoenix használata Linux-alapú Apache HBase-fürtökkel a HDInsight-ben](../hbase/apache-hbase-query-with-phoenix.md)
* [Sózott táblák](https://phoenix.apache.org/salted.html)
* [Apache Phoenix nyelvtan](https://phoenix.apache.org/language/index.html)
