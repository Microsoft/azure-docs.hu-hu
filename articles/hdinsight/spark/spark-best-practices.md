---
title: Apache Spark irányelvek az Azure HDInsight
description: Útmutató a Apache Spark Azure HDInsight való használatához.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938812"
---
# <a name="apache-spark-guidelines"></a>Apache Spark irányelvek

Ez a cikk a Apache Spark Azure HDInsight való használatának különböző irányelveit ismerteti.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hogyan a Spark-feladatok futtatását vagy elküldését?

| Beállítás | Dokumentumok |
|---|---|
| VSCode | [A Spark &-struktúra eszközeinek használata a Visual Studio Code-hoz](../hdinsight-for-vscode.md) |
| Jupyter-notebookok | [Oktatóanyag: Adatok betöltése és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsightban](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Oktatóanyag: Azure Toolkit for IntelliJ használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Oktatóanyag: Scala Maven-alkalmazás létrehozása a HDInsight-hez Apache Spark a IntelliJ használatával](./apache-spark-create-standalone-application.md) |
| Zeppelin-notebookok | [Apache Zeppelin-notebookok használata Apache Spark-fürtökön az Azure HDInsight rendszerében](./apache-spark-zeppelin-notebook.md) |
| Távoli feladatok beküldése a Livy | [Az Apache Spark REST API használata távoli feladatok küldéséhez egy HDInsight Spark-fürtre](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|A Oozie olyan munkafolyamat-és koordinációs rendszer, amely a Hadoop-feladatokat kezeli.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|A Livy használatával interaktív Spark-rendszerhéjakat futtathat, vagy elküldheti a Spark-on futó kötegelt feladatokat.|
|[Apache Spark Azure Data Factory](../../data-factory/transform-data-using-spark.md)|Egy Data Factory folyamat Spark-tevékenysége egy Spark-programot hajt végre a saját vagy [igény szerinti HDInsight-fürtön.|
|[Apache Hive Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md)|A Data Factory folyamat HDInsight-struktúrájának tevékenysége a saját vagy igény szerinti HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hogyan a Spark-feladatok figyelését és hibakeresését?

| Beállítás | Dokumentumok |
|---|---|
| Azure Toolkit for IntelliJ | [Hiba a Spark-feladatok hibakereséséhez Azure Toolkit for IntelliJ (előzetes verzió)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ SSH-n keresztül | [HDInsight-fürtön futó Apache Spark-alkalmazások helyi vagy távoli hibakeresése az IntelliJ-hez készült Azure-eszközkészlettel SSH-n keresztül](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ VPN-en keresztül | [Apache Spark-alkalmazások távoli hibakeresése a HDInsight a VPN-en keresztül a Azure Toolkit for IntelliJ használatával](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| A Apache Spark History Serveren futó feladatok gráfja | [Kiterjesztett Apache Spark-előzménykiszolgáló használata az Apache Spark-alkalmazások hibakereséséhez és diagnosztizálásához](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hogyan, hogy a Spark-feladatok hatékonyabban futnak?

| Beállítás | Dokumentumok |
|---|---|
| IO-gyorsítótár | [Apache Spark számítási feladatok teljesítményének javítása az Azure HDInsight IO cache (előzetes verzió) használatával](./apache-spark-improve-performance-iocache.md) |
| Beállítási lehetőségek | [Az Apache Spark-feladatok optimalizálása](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hogyan csatlakozni más Azure-szolgáltatásokhoz?

| Beállítás | Dokumentumok |
|---|---|
| Apache Hive a HDInsight | [Apache Spark és Apache Hive integrálása a méhkas Warehouse-összekötővel](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight-alapú Apache HBase | [Az Apache Spark használata Apache HBase-adatok írására és olvasására](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka a HDInsight | [Oktatóanyag: Az Apache Spark strukturált stream használata az Apache Kafkával a HDInsighton](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB-hez készült Azure Synapse Link](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Mik a tárolási lehetőségeim?

| Beállítás | Dokumentumok |
|---|---|
| 2. generációs Azure Data Lake Storage | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| 1. generációs Azure Data Lake Storage | [Azure Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Storage | [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Következő lépések

* [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)
* [Apache Spark feladatok optimalizálása a HDInsight-ben](apache-spark-perf.md)
