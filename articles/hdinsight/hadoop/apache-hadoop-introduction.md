---
title: Mi az Apache Hadoop és a MapReduce – Azure HDInsight
description: Bevezetés a HDInsight, valamint a Apache Hadoop Technology stackbe és összetevőkbe.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 1e3fc23dd220a8d9764a64c453e9fb856307ac47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946516"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Mi Apache Hadoop az Azure HDInsight?

Az [Apache Hadoop](https://hadoop.apache.org/) volt a big data jellegű adatkészletek fürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. A Hadoop-ökoszisztéma olyan kapcsolódó szoftvereket és segédprogramokat tartalmaz, mint például az Apache Hive, az Apache HBase, a Spark, a Kafka és sok más.

Az Azure HDInsight egy teljes körűen felügyelt, teljes spektrumú, nyílt forráskódú elemzési szolgáltatás a felhőben a vállalatok számára. Az Azure HDInsight Apache Hadoop-fürt típusa lehetővé teszi a [Apache Hadoop elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), [Apache Hadoop fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) -erőforrás-kezelés és egy egyszerű [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programozási modell használatát, amellyel párhuzamosan dolgozható fel és elemezhető a Batch-adatokat.  A HDInsight-ben található Hadoop-fürtök kompatibilisek az [Azure Blob Storage](../../storage/common/storage-introduction.md), a [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md)vagy a [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

A HDInsighton elérhető Hadoop-technológiák összetevőinek megtekintése: [A HDInsighttal elérhető összetevők és verziók](../hdinsight-component-versioning.md). További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a name="what-is-mapreduce"></a>Mi az a MapReduce?

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) olyan szoftver-keretrendszer, amely nagy mennyiségű adat feldolgozására szolgáló feladatokat ír. A bemeneti adatok felosztása független adattömbökbe történik. Minden adathalmaz feldolgozása párhuzamosan történik a fürt csomópontjai között. A MapReduce-feladatok két függvényből állnak:

* **Mapper**: a bemeneti adatokat használja, elemzi (általában szűrési és rendezési műveletekkel), és rekordok (kulcs-érték párok) bocsát ki.

* **Szűkítő**: felhasználja a Mapper által kibocsátott rekordok, és olyan összegző műveletet hajt végre, amely létrehoz egy kisebb, kombinált eredményt a Mapper adataiból.

A következő ábrán látható egy alapszintű Word Count MapReduce-feladat példája:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Ennek a feladatoknak a kimenete annak a száma, hogy az egyes szavak hányszor fordultak elő a szövegben.

* A Mapper a bemeneti szöveg minden sorát bemenetként veszi át, és megszakítja a szavakat. Egy kulcs/érték párokat bocsát ki, minden alkalommal, amikor egy szó bekövetkezik a szót, majd egy 1. A kimenet rendezése megtörténik, mielőtt elküldi azt a redukáló felé.
* A szűkítő összesíti ezeket az egyes szavakat, és egyetlen kulcs/érték párokat bocsát ki, amely a szót az előfordulások összege követve tartalmazza.

A MapReduce több nyelven is megvalósítható. A Java a leggyakoribb implementáció, és a jelen dokumentumban bemutató célokra szolgál.

## <a name="development-languages"></a>Fejlesztési nyelvek

A Java és a Java virtuális gép alapú nyelvek vagy keretrendszerek közvetlenül [MapReduce-feladatokként](..//hadoop/submit-apache-hadoop-jobs-programmatically.md)futtathatók. A dokumentumban használt példa egy Java MapReduce alkalmazás. A nem Java nyelveken, például a C#, a Python vagy az önálló végrehajtható fájlok esetében a **Hadoop streaming** használatát kell használnia.

A Hadoop stream a leképező és a redukáló adatátvitelt az STDIN és az STDOUT fölé továbbítja. A leképező és a redukáló adatokat tartalmazó sort olvas az STDIN-ből, és a kimenetet az STDOUT értékre írja. A leképező és a szűkítő által beolvasott vagy kibocsátott soroknak a TAB karakterrel tagolt kulcs/érték párok formátumában kell szerepelniük:

`[key]\t[value]`

További információ: [Hadoop streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Az Hadoop streaming HDInsight-vel való használatát bemutató példákért tekintse meg a következő dokumentumot:

* [C# MapReduce-feladatok fejlesztése](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Hol kezdjem el

* [Rövid útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsight Azure Portal használatával](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Oktatóanyag: Apache Hadoop feladatok elküldése a HDInsight-ben](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Java MapReduce-programok fejlesztése a HDInsight Apache Hadoop](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Apache Hive használata kinyerési, átalakítási és betöltési (ETL) eszközként](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Kinyerés, átalakítás és betöltés (ETL) skálán](../hadoop/apache-hadoop-etl-at-scale.md)
* [Adatelemzési folyamat üzembe helyezése](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Következő lépések

* [Apache Hadoop-fürt létrehozása a HDInsight a portál használatával](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Apache Hadoop-fürt létrehozása a HDInsight ARM-sablon használatával](../hadoop/apache-hadoop-linux-tutorial-get-started.md)