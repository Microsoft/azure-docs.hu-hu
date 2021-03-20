---
title: Az Apache beines használata a Apache Hive-Azure HDInsight
description: Ismerje meg, hogyan futtathat kaptár-lekérdezéseket a Hadoop-mel a HDInsight-on a Beeline-ügyfél használatával. A Beeline egy olyan segédprogram, amely a HiveServer2-t a JDBC protokollon keresztül folytatja.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943202"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Az Apache Beeline-ügyfél használata Apache Hive-val

Ez a cikk azt ismerteti, hogyan használható a parancssori [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) -ügyfél Apache Hive lekérdezések létrehozásához és végrehajtásához SSH-kapcsolaton keresztül.

## <a name="background"></a>Háttér

A Beeline egy kaptár-ügyfél, amely a HDInsight-fürt fő csomópontjain található. Ha a HDInsight-fürtön telepített Beeline-ügyfélhez szeretne csatlakozni, vagy helyileg szeretné telepíteni a telepítést, tekintse meg az [Apache Beeline vagy az üzembe](connect-install-beeline.md)helyezés telepítése című témakört. A Beeline JDBC használatával csatlakozik a HDInsight-fürtön üzemeltetett HiveServer2-hez. A Beeline használatával távolról is elérheti a HDInsight a struktúrát az interneten keresztül. A következő példák a HDInsight való kapcsolódáshoz használt leggyakoribb kapcsolati karakterláncokat biztosítják a Beeline szolgáltatásból.

## <a name="prerequisites-for-examples"></a>Példák az előfeltételekre

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* Figyelje meg a fürt elsődleges tárolójának URI-sémáját. Például  `wasb://` Az Azure Storage-hoz, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1hoz. Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, az URI a következő: `wasbs://` . További információ: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md). A jelen dokumentumban ismertetett lépések többsége azt feltételezi, hogy egy SSH-munkamenetből a fürtre használja a Beeline-t. Használhat helyi Beeline-ügyfelet is, de ezek a lépések nem szerepelnek ebben a cikkben.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Ez a példa a Beeline-ügyfél SSH-kapcsolatban való használatára épül.

1. Nyisson meg egy SSH-kapcsolódást a fürthöz az alábbi kóddal. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A következő parancs beírásával csatlakozhat a HiveServer2-hez a Beeline-ügyféllel az Open SSH-munkamenetből:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. A Beeline parancsok `!` karakterrel kezdődnek, például `!help` megjeleníti a súgót. `!`Bizonyos parancsok esetében azonban a kihagyható. Például a `help` is működik.

    Létezik `!sql` , amely a HiveQL utasítások végrehajtásához használatos. A HiveQL azonban általában úgy használják, hogy kihagyhatja a fentieket `!sql` . A következő két utasítás egyenértékű:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Egy új fürtön csak egy tábla jelenik meg: **hivesampletable**.

4. A hivesampletable sémájának megjelenítéséhez használja a következő parancsot:

    ```hiveql
    describe hivesampletable;
    ```

    Ez a parancs a következő információkat adja vissza:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Ez az információ a tábla oszlopait ismerteti.

5. Adja meg a következő utasításokat a **log4jLogs** nevű tábla létrehozásához a HDInsight-fürthöz megadott mintaadatok használatával: (szükség szerint módosítsa az URI-séma alapján.)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Ezek az utasítások a következő műveleteket végzik el:

    |Utasítás |Leírás |
    |---|---|
    |DROP TABLE|Ha a tábla létezik, törölve lett.|
    |KÜLSŐ TÁBLA LÉTREHOZÁSA|Létrehoz egy **külső** táblát a struktúrában. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.|
    |SOR FORMÁTUMA|Az adat formázása. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.|
    |TEXTFILE HELYEN TÁROLVA|Az adattárolás helye és a fájl formátuma.|
    |SELECT|Kiválasztja az összes olyan sor számát, ahol a **T4** oszlop tartalmazza a **[hiba]** értéket. Ez a lekérdezés **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.|
    |INPUT__FILE__NAME például: "%. log"|A struktúra megpróbálja alkalmazni a sémát a címtárban található összes fájlra. Ebben az esetben a könyvtár olyan fájlokat tartalmaz, amelyek nem egyeznek a sémával. Ha meg szeretné akadályozni, hogy az eredmények ne kerüljenek az adatokba, ez az utasítás azt ismerteti, hogy a kaptár csak a. log fájlban végződő fájlokból tér vissza.|

   > [!NOTE]  
   > Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy automatizált adatfeltöltési folyamat vagy egy MapReduce művelet.
   >
   > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

    A parancs kimenete az alábbi szöveghez hasonló:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Kilépés Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL-fájl futtatása

Ez a példa az előző példa folytatását szemlélteti. A következő lépésekkel hozzon létre egy fájlt, majd futtassa a Beeline paranccsal.

1. A következő parancs használatával hozzon létre egy **query. HQL** nevű fájlt:

    ```bash
    nano query.hql
    ```

1. Használja a következő szöveget a fájl tartalmának megfelelően. Ez a lekérdezés egy új, **alkalmazásnaplókat** nevű belső táblát hoz létre:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások a következő műveleteket végzik el:

    |Utasítás |Leírás |
    |---|---|
    |CREATE TABLE, HA NEM LÉTEZIK|Ha a tábla még nem létezik, akkor létrejön. Mivel a **külső** kulcsszó nincs használatban, az utasítás belső táblát hoz létre. A belső táblák a struktúra adattárházában tárolódnak, és a struktúra teljes mértékben felügyeli őket.|
    |ORK-KÉNT TÁROLVA|Az adatok az optimalizált sorok oszlopos (ORK) formátumban vannak tárolva. Az ork formátum a kaptárak adatok tárolására szolgáló, kiválóan optimalizált és hatékony formátum.|
    |FELÜLÍRÁS BESZÚRÁSA... Válassza|A **[Error]** karaktert tartalmazó sorok kiválasztása a **log4jLogs** táblából, majd az adatok beillesztése a **alkalmazásnaplókat** táblába.|

    > [!NOTE]  
    > A külső tábláktól eltérően a belső tábla eldobása a mögöttes adatokat is törli.

1. A fájl mentéséhez használja a **CTRL X billentyűkombinációt**, + majd írja be az **Y** értéket, és végül **írja be** a következőt:.

1. A következő paranccsal futtathatja a fájlt a Beeline használatával:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > A `-i` paraméter elindítja és futtatja a fájlban szereplő utasításokat `query.hql` . A lekérdezés befejeződése után a rendszer `jdbc:hive2://headnodehost:10001/>` megkéri a kérdést. A (z) paraméterrel is futtathat egy fájlt `-f` , amely a lekérdezés befejeződése után kilép a szolgáltatásból.

1. A **alkalmazásnaplókat** tábla létrehozásának ellenőrzéséhez használja a következő utasítást a **alkalmazásnaplókat** összes sorának visszaküldéséhez:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Három sornyi értéket kell visszaadnia, mind a (z **) [Error]** értéket tartalmazza a T4 oszlopban:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Következő lépések

* További általános információk a HDInsight-beli Kaptárról: [Apache Hive használata a Apache Hadoop használatával a HDInsight](hdinsight-use-hive.md)

* További információk a HDInsight-ben végzett Hadoop-használattal kapcsolatos egyéb módszerekről: [a MapReduce használata az Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)
