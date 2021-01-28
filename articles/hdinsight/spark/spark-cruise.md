---
title: A SparkCruise használata az Azure HDInsight Apache Spark lekérdezések felgyorsításához
description: Megtudhatja, hogyan használhatja a SparkCruise optimalizálási platformot Apache Spark lekérdezések hatékonyságának növelése érdekében.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f07ea3007d2e72c179abe38cca56178b564bc231
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928271"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise az Azure HDInsight

Ez a dokumentum az Azure HDInsight szolgáltatás *SparkCruise* ismerteti, amely automatikusan újrahasznosítja Apache Spark számításokat a lekérdezés hatékonyságának növelése érdekében.

## <a name="overview"></a>Áttekintés

Az olyan elemzési platformon futtatott lekérdezések, mint például a Apache Spark, egy kisebb allekérdezéseket tartalmazó lekérdezési tervbe vannak kibontva. Ezek az allekérdezések többször is megjelennek a lekérdezési csomagok között több lekérdezés esetében. Minden alkalommal, amikor azok bekövetkeznek, újra végre lesznek hajtva az eredmények visszaküldéséhez. A lekérdezés ismételt végrehajtása azonban nem hatékony, és szükségtelen számítási költségekhez vezethet.

A *SparkCruise* olyan számítási feladatok optimalizálási platformja, amely felhasználhatja a gyakori számításokat, csökkentve a lekérdezés végrehajtásának teljes idejét és az adatátviteli költségeket. A platform egy *anyagbeli nézet* fogalmát használja, amely egy olyan lekérdezés, amelynek az eredményei előre számított formában vannak tárolva. Ezek az eredmények ezután újra felhasználhatók, ha a lekérdezés később ismét megjelenik, ahelyett, hogy újra feldolgozta az eredményeket.

## <a name="setup-and-installation"></a>Beállítás és telepítés

A SparkCruise az összes HDInsight 4,0-fürtön elérhető a Spark 2,3 vagy 2,4. A SparkCruise könyvtár fájljai a `/opt/peregrine/` HDInsight-fürt könyvtárába vannak telepítve. Ahhoz, hogy megfelelően működjön, a *SparkCruise* a következő konfigurációs tulajdonságokat igényli, amelyek alapértelmezés szerint be vannak állítva.

* `spark.sql.queryExecutionListeners` beállítás értéke `com.microsoft.peregrine.spark.listeners.PlanLogListener` , amely lehetővé teszi a lekérdezési csomagok naplózását.
* `spark.sql.extensions` értékre van állítva `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , amely lehetővé teszi az online megvalósításának gyakorlatban és az újbóli használatra vonatkozó optimalizáló szabályokat.

## <a name="computation-reuse-in-spark-sql"></a>Számítás ismételt felhasználása a Spark SQL-ben

Az alábbi példa azt szemlélteti, hogyan használható a *SparkCruise* az Apache Spark lekérdezések optimalizálására. 

1. SSH-t a Spark-fürt fő csomópontjához.
1. Gépelje be: `spark-shell`.
1. Futtassa az alábbi kódrészletet, amely néhány alapvető lekérdezést futtat a fürtön található mintaadatok használatával.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. A *SparkCruise* lekérdezés-elemzési eszközzel elemezheti az előző lekérdezések lekérdezési terveit, amelyek a Spark-alkalmazás naplófájljaiban tárolódnak. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Megtekintheti az elemzési folyamat kimenetét, amely egy visszajelzési fájl. Ez a visszajelzési fájl megjegyzéseket tartalmaz a jövőbeli Spark SQL-lekérdezésekhez. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

A `analyze` parancs elemzi a lekérdezési terveket, és létrehozza a munkaterhelés táblázatos megjelenítését. Ez a munkaterhelés-táblázat a [HDInsight SparkCruise-minták](https://github.com/Azure-Samples/azure-sparkcruise-samples) tárházában található *WorkloadInsights* notebook használatával kérdezhető le. Ezután a `views` parancs azonosítja az általános altervi kifejezéseket, és kijelöli a jövőbeli megvalósításának gyakorlatban és újbóli felhasználásának érdekes altervi kifejezéseit. A kimenet egy visszajelzési fájl, amely megjegyzéseket tartalmaz a jövőbeli Spark SQL-lekérdezésekhez. 

A `show` parancs az alábbi szöveghez hasonló kimenetet jelenít meg:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

A visszajelzési fájl a következő formátumban tartalmazza a bejegyzéseket: `subplan-identifier [Materialize|Reuse] input/path/to/action` . Ebben a példában két egyedi aláírás létezik: az egyik az első két ismétlődő lekérdezés, a második pedig az utolsó két lekérdezésben a szűrő predikátumot jelképezi. Ezzel a visszajelzési fájllal a következő lekérdezések automatikusan megvalósul, és újra felhasználhatják a közös alcsomagokat. 

Az optimalizálások teszteléséhez hajtsa végre a minta-lekérdezések egy másik készletét.

1. Gépelje be: `spark-shell`.
1. A következő kódrészlet végrehajtása

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. A visszajelzési fájl ismételt megtekintésével megtekintheti az újból felhasznált lekérdezések aláírásait.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

A `show` parancs az alábbi szöveghez hasonló kimenetet ad vissza:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Bár a lekérdezésben szereplő literál érték a verzióról értékre változott `'11%'` `'12%'` , a *SparkCruise* továbbra is egyeztetheti a korábbi lekérdezéseket olyan új lekérdezésekkel, amelyek kisebb eltéréseket tartalmaznak, mint például a literális értékek és az adatkészlet-verziók evolúció Ha a lekérdezés jelentős változtatásokat hajt végre, újra futtathatja az elemzési eszközt az újból felhasználható új lekérdezések azonosításához.

A színfalak mögött a *SparkCruise* elindít egy allekérdezést a kiválasztott altervnek az azt tartalmazó első lekérdezésből való megvalósulni. A később lekérdezett lekérdezések nem módosítják őket. Ebben a számítási feladatban az altervek az első és a harmadik lekérdezés által online módon lesznek bevezetve. A lekérdezések változását a közös alcsomagok betartása után láthatjuk.

Láthatja, hogy most már négy új, a következő lekérdezésekben újra felhasználható alkifejezés található.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A visszajelzési fájlok, az anyagbeli altervek és a lekérdezési naplók megmaradnak a Spark-munkamenetek között. A fájlok eltávolításához futtassa a következő parancsot:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Következő lépések

* [A SparkCruise előnyeinek meghatározása a számítási feladatok beolvasása jegyzetfüzet használatával](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Apache Spark számítási feladatok teljesítményének javítása az Azure HDInsight IO cache használatával](apache-spark-improve-performance-iocache.md)
* [Apache Spark feladatok optimalizálása a HDInsight-ben](./apache-spark-perf.md)
* [SparkCruise: a Sparkban használatos a kihangosító számítási felhasználása](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Apache Spark irányelvek az Azure HDInsight](./spark-best-practices.md)
