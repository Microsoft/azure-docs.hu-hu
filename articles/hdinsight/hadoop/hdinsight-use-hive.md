---
title: Mi az Apache Hive és a HiveQL – Azure HDInsight
description: Az Apache Hive-egy rendszer data warehouse az Apache Hadoop. A Hive használata a HiveQL, tárolt adatok lekérdezheti, amely hasonló Transact-SQL. Ebből a dokumentumból megtudhatja, hogyan használata Azure HDInsight Hive és a HiveQL.
keywords: hiveql, mi az a hive, a hadoop hiveql, hogyan használható a hive, ismerje meg a hive, mi az a hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2019
ms.openlocfilehash: 1f0746436fa980b6becfa7a88560734aa07a54e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342831"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Mi az Apache Hive és az Azure HDInsight HiveQL?

[Az Apache Hive](https://hive.apache.org/) egy rendszer data warehouse az Apache Hadoop. Hive adatösszegzéseket, lekérdezéséhez és az adatok elemzése lehetővé teszi. Hive-lekérdezések HiveQL, amely egy hasonló, az SQL-lekérdezési nyelv nyelven íródtak.

Hive teszi projektstruktúra nagymértékben strukturálatlan adatokon. Struktúráját határozza meg, miután a HiveQL használatával lekérdezheti az adatokat, a Java vagy a MapReduce ismerete nélkül.

HDInsight fürt számos különböző, amelyek ideálisak az adott munkaterhelés konkrét biztosít. A következő típusú fürtöket leggyakrabban a Hive-lekérdezések használják:

* __Interaktív lekérdezés__: Egy Hadoop-fürt által biztosított [alacsony késés analitikus feldolgozás (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) interaktív lekérdezések válaszidők javításának funkciót. További információkért lásd: a [a HDInsight interaktív lekérdezések használatának első lépései](../interactive-query/apache-interactive-query-get-started.md) dokumentumot.

* __Hadoop__: Egy Hadoop-fürtöt, amely kötegelt számítási feladatok feldolgozásához van beállítva. További információkért lásd: a [indítsa el a HDInsight Apache Hadoop-keretrendszerrel](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentumot.

* __A Spark__: Az Apache Spark rendelkezik beépített funkcióval használata a Hive segítségével. További információkért lásd: a [indítsa el az Apache Spark on HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentumot.

* __A HBase__: HiveQL használható az Apache HBase tárolt adatokat lekérdezni. További információkért lásd: a [indítsa el az Apache HBase a HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentumot.

## <a name="how-to-use-hive"></a>Hive használata

Használja az alábbi táblázat a Hive használata a HDInsight különböző módjainak felderítéséhez:

| **Ezzel a módszerrel** Ha azt szeretné... | ... **interaktív** lekérdezések | ...**kötegelt** feldolgozása | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X vagy Windows |
| [HDInsight tools for Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive-nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Bármely (böngésző alapú) |
| [A beeline-ügyfél](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X vagy Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X vagy Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |


## <a name="hiveql-language-reference"></a>HiveQL nyelvi referencia

HiveQL nyelvi dokumentáció áll rendelkezésre a [nyelv manuális (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive-val és az adatok struktúrája

Hive tisztában van azzal, hogyan használható a strukturált és részben strukturált adatok. Például szöveges fájlok, a mezők különleges karakterek vannak elválasztva. A következő HiveQL-utasítás szóközzel elválasztott adatok egy táblát hoz létre:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive is támogatja az egyéni **szerializáló/deserializers (SerDe)** túl összetett vagy szabálytalan strukturált adatok számára. További információkért lásd: a [egy egyéni JSON-SerDe használata a HDInsight](https://web.archive.org/web/20190217104719/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) dokumentumot.

A Hive által támogatott fájlformátumok további információkért lásd: a [nyelv manuális ()https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Hive-táblák belső és külső táblák

Olyan táblázatot, amely a Hive használatával is létrehozhat két típusa van:

* __Belső__: A Hive-adatraktárban tárolt adatok. Az adattárház a következő helyen található `/hive/warehouse/` a fürt alapértelmezett tárolására.

    Belső táblák használata esetén a következő feltételek valamelyike a alkalmazni:

    * Adatok csak átmenetileg létezik.
    * Azt szeretné, hogy a Hive a táblát és az adatok életciklusának kezelésére.

* __Külső__: Az adatraktár kívüli adatokat tárolja. Az adatok a fürt által elérhető minden tárterület is tárolhatja.

    Külső táblák használata esetén a következő feltételek valamelyike a alkalmazni:

    * Az adatok Hive-en kívül is használható. Például frissítése az adatfájlok egy másik folyamat (azaz nem zárolja a fájlokat.)
    * Adatok kell a mögöttes helyre, a tábla eldobása után is megmaradnak.
    * Szüksége lesz egy egyéni helyen, például egy nem alapértelmezett tárfiók.
    * Nem a hive kezeli a adatformátum, tartózkodási hely, stb.

További információkért lásd: a [Hive belső és külső táblák bevezető] [ cindygross-hive-tables] blogbejegyzést.

## <a name="user-defined-functions-udf"></a>Felhasználó által definiált függvények (UDF)

Hive is kiterjeszthető keresztül **felhasználó által definiált függvények (UDF)**. Egy UDF funkciók vagy logika, amely nem könnyen modellezett bevezetése a HiveQL teszi lehetővé. Például egy UDF-EK használata a Hive az alábbi dokumentumokban talál:

* [Az Apache Hive egy Java-felhasználó által definiált függvény használata](../hadoop/apache-hadoop-hive-java-udf.md)

* [Az Apache Hive egy Python-felhasználó által definiált függvény használata](../hadoop/python-udf-hdinsight.md)

* [Használja a C# az Apache Hive-, felhasználó által definiált függvény](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight egyéni Apache Hive-felhasználó által definiált függvény hozzáadása](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Például az Apache Hive felhasználói függvény dátum/idő formátumot átalakítása a Hive-időbélyeg](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Példa adatok

A HDInsight Hive előre betöltött tartalmaz egy belső tábla nevű `hivesampletable`. HDInsight Hive-val használható példa adatkészleteket is biztosít. Ezek az adatkészletek vannak tárolva a `/example/data` és `/HdiSamples` könyvtárak. Ezek a könyvtárak a a fürt alapértelmezett tárolója szerepel.

## <a id="job"></a>Példa Hive-lekérdezés

A következő hiveql-projekt oszlopok az alakzatot a `/example/data/sample.log` fájlt:

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
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
    GROUP BY t4;
```

Az előző példában a hiveql hajtsa végre a következő műveleteket:


* `DROP TABLE`: Ha a tábla már létezik, törölje azt.

* `CREATE EXTERNAL TABLE`: Létrehoz egy új **külső** Hive táblát. Külső táblák csak tárolja a tábla definíciójának Hive. Az adatok az eredeti helyre, és az eredeti formátumban marad.

* `ROW FORMAT`: Arra utasítja a Hive, az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.

* `STORED AS TEXTFILE LOCATION`: Arra utasítja a Hive, az adatok tárolására (a `example/data` directory), és hogy szövegként lesz tárolva. Az adatok egyetlen fájlban vagy több fájl a könyvtárban lévő elosztva.

* `SELECT`: Kiválasztja az összes sor számát, az oszlop **t4** értéket tartalmazza **[hiba]**. A jelen nyilatkozat egy értékét adja vissza **3** mert három sort, amely tartalmazza ezt az értéket.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive megkísérli a alkalmazni a sémát a címtárban található összes fájl. Ebben az esetben a könyvtárban található fájlok, amelyek nem egyeznek a sémát. Szemétgyűjtési adatokat a eredmények elkerülése érdekében a jelen nyilatkozat arra utasítja a Hive, hogy azt kell csak adatokat adja vissza a végződésű fájlokból. napló.

> [!NOTE]  
> Külső táblák kell használni, amikor várhatóan az alapul szolgáló adatokat egy külső forrás frissíteni kell. Például az automatikus feltöltési folyamat, vagy a MapReduce művelet.
>
> A külső tábla elvetését does **nem** törölheti az adatokat, csak törli a tábla definícióját.

Hozhat létre egy **belső** helyett külső táblát, a következő hiveql-lel:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Ezek az utasítások hajtsa végre a következő műveleteket:

* `CREATE TABLE IF NOT EXISTS`: Ha a tábla nem létezik, hozza létre. Mivel a **külső** kulcsszó nem használható, ez az utasítás létrehoz egy belső táblázatban. A tábla a Hive-adatraktárban tárolja, és Hive teljes egészében kezeli.

* `STORED AS ORC`: Optimalizált sor Oszlopalapú (ORC) formátumban tárolja az adatokat. ORC Hive-adatok tárolására szolgáló nagymértékben optimalizált és hatékony formátumban.

* `INSERT OVERWRITE ... SELECT`: A sorok kiválasztása a **log4jLogs** tartalmazó tábla **[hiba]**, majd beszúrja az adatokat, és a **hibanaplókat** tábla.

> [!NOTE]  
> Ellentétben a külső táblák elvetését egy belső táblát is törli az alapul szolgáló adatokat.

## <a name="improve-hive-query-performance"></a>Hive-lekérdezések teljesítményének növelése

### <a id="usetez"></a>Apache Tez

[Az Apache Tez](https://tez.apache.org) egy keretrendszer, amely lehetővé teszi, hogy az adatok nagy számításigényű alkalmazásokat, például a Hive, sokkal hatékonyabban futtatásához ipari méretekben. Alapértelmezés szerint engedélyezve van a tezben futtatja.  A [Apache Hive Tez tervezési dokumentumok](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) megvalósítási és hangolási konfiguráció részleteit tartalmazza.

### <a name="low-latency-analytical-processing-llap"></a>Közel valós idejű analitikus feldolgozás (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (más néven hosszú és eredményes), amely lehetővé teszi, hogy a memóriában történő gyorsítótárazás lekérdezések Hive 2.0 új szolgáltatása. LLAP teszi akár sokkal gyorsabb Hive-lekérdezések [26 x gyorsabb, mint a Hive-1.x bizonyos esetekben](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight interaktív lekérdezési fürt típusban LLAP biztosít. További információkért lásd: a [interaktív lekérdezések használatának első lépései](../interactive-query/apache-interactive-query-get-started.md) dokumentumot.

## <a name="scheduling-hive-queries"></a>Hive-lekérdezések ütemezése

Nincsenek számos szolgáltatás használható a Hive-lekérdezések futtatásához egy ütemezett vagy igény szerinti munkafolyamat részeként.

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory lehetővé teszi, hogy HDInsight a Data Factory-folyamatok részeként. További információ a Hive használatával egy folyamatból, tekintse meg a [adatok átalakítása a Hive-tevékenység használatával az Azure Data Factoryban](../../data-factory/transform-data-using-hadoop-hive.md) dokumentumot.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-feladatok és az SQL Server Integration Services

Az SQL Server Integration Services (SSIS) használatával egy Hive-feladat futtatása. Az Azure Feature Pack for SSIS biztosít a következő összetevőket, amely a HDInsight Hive-feladatok dolgozhat.

* [Az Azure HDInsight Hive-tevékenység][hivetask]

* [Azure Subscription Connection Manager][connectionmanager]

További információkért lásd: a [Azure Feature Pack] [ ssispack] dokumentációját.

### <a name="apache-oozie"></a>Az Apache Oozie

Az Apache Oozie egy rendszer munkafolyamat és összehangoláshoz, amely a Hadoop-feladatokat kezeli. Az Oozie használata a Hive használatával további információkért lásd: a [használata Apache Oozie megadásához és a munkafolyamat futtatása](../hdinsight-use-oozie-linux-mac.md) dokumentumot.

## <a id="nextsteps"></a>Következő lépések

Most, hogy már Hive van, és hogyan használható a HDInsight a Hadoop-keretrendszerrel, egyéb módon az Azure HDInsight használata az alábbi hivatkozások segítségével.

* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [Az Apache Pig használata a HDInsight][hdinsight-use-pig]
* [HDInsight MapReduce-feladatok használata][hdinsight-use-mapreduce]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: https://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
