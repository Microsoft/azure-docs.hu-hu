---
title: Azure HDInsight – hibaelhárítási útmutatók
description: Apache Hadoop munkaterhelések hibakeresése az Azure HDInsight használatával. A részletes dokumentációból megtudhatja, hogyan használhatja a HDInsight-t a Apache Hive, a Apache Spark, az Apache-fonal, az Apache HBase, a HDFS és a Apache Storm gyakori problémáinak megoldásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 3578cda1b51dbb70b0bdfebf92b98c3fa7aa2842
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085956"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Hibák az Azure HDInsight használatával

| Apache munkaterhelés | A leggyakoribb kérdések |
|---|---|
|![hdinsight Apache HBase ikon ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Az Apache HBase hibaelhárítása](hbase/apache-troubleshoot-hbase.md)|<br>[Nem hozzárendelt régiók](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[A hbase hbck parancs időtúllépése az Azure HDInsightban](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Kapcsolódási problémák Apache Phoenix az Azure HDInsight](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[Mi okozza, hogy a főkiszolgáló nem indul el?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException – már használatban van a címe](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![hdinsight Apache hdfs ikon ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Apache Hadoop HDFS hibáinak megoldása](hdinsight-troubleshoot-hdfs.md)|<br>[Hogyan hozzáférhet egy helyi HDFS egy fürtön belülről?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Az Azure HDInsight-fürtön biztonságos módban ragadt helyi HDFS](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![hdinsight Apache kaptár ikon ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[Hibakeresés Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Hogyan exportáljon egy Hive-metaadattár, és importálja egy másik fürtre?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Hogyan megkeresni Apache Hive naplókat a fürtön?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Hogyan elindítja a Apache Hive-rendszerhéjt a fürt adott beállításaival?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Hogyan az Apache TEZ DAG-alapú adatelemzést a fürt kritikus elérési útjára?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Hogyan letölti az Apache TEZ DAG-adatait egy fürtről?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![hdinsight Apache Spark ikon ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Hibakeresés Apache Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Hogyan konfigurálható egy Apache Spark-alkalmazás az Apache Ambari fürtökön történő használatával?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[Hogyan konfigurálható egy Apache Spark-alkalmazás egy Jupyter Notebook-jegyzetfüzet fürtökön történő használatával?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Hogyan konfigurálható egy Apache Spark-alkalmazás az Apache Livy fürtökön történő használatával?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[Hogyan konfigurálható egy Apache Spark-alkalmazás a spark-submit fürtökön történő használatával?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[Hogyan konfiguráljon egy Apache Spark alkalmazást a IntelliJ használatával?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Hogyan konfiguráljon egy Apache Spark alkalmazást az Eclipse használatával?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Hogyan konfiguráljon egy Apache Spark alkalmazást a VSCode használatával?](hdinsight-for-vscode.md)<br><br>[Működése OutOfMemoryError kivétel Apache Spark](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![hdinsight Apache Storm ikon ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Hibakeresés Apache Storm](hdinsight-troubleshoot-STORM.md)|<br>[Hogyan fér hozzá a Apache Storm felhasználói felületéhez a fürtön?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Hogyan az Event hub kiöntő ellenőrzőpont-információit az egyik topológiáról a másikra Apache Storm](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Hogyan megkeresni a Storm bináris fájljait egy fürtön?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Hogyan megállapítani egy Storm-fürt telepítési topológiáját?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Hogyan keresse meg Apache Storm Event hub kiöntő bináris fájljait a fejlesztéshez?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![hdinsight Apache-szál ikon ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Apache Hadoop FONALak hibáinak megoldása](hdinsight-troubleshoot-YARN.md)|<br>[Hogyan hozzon létre egy új Apache Hadoop FONÁL-várólistát egy fürtön?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Hogyan letölteni a Apache Hadoop a FONALak naplóit a fürtből?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight-hibaelhárítási erőforrások

| További információ | Lásd a következő cikkeket: |
| --- | --- |
| HDInsight Linuxon és optimalizáláson | - [Információk a HDInsight Linuxon való használatáról](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop memória és teljesítmény hibaelhárítása](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive lekérdezési teljesítmény](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Naplók és memóriaképek | - [Hozzáférési Apache Hadoop a FONALat futtató alkalmazások naplóihoz Linux rendszeren](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Halom-memóriaképek engedélyezése a Linuxon futó Apache Hadoop-szolgáltatásokhoz](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)|
| Hibák | - [Webhcaten-hibák megértése és megoldása](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [OutofMemory-hiba elhárításának Apache Hive beállításai](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Eszközök | - [Apache Hive lekérdezések optimalizálása](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ eszköz](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse eszköz](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode eszköz](hdinsight-for-vscode.md)<br>- [HDInsight Visual Studio-eszköz](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
