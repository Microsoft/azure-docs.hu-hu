---
title: A kaptár hibáinak megoldása az Azure HDInsight
description: Választ kaphat a Apache Hive és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
keywords: Azure HDInsight, struktúra, GYIK, hibaelhárítási útmutató, gyakori kérdések
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895234"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Az Apache Hive hibaelhárítása az Azure HDInsighttal

Ismerkedjen meg a legfontosabb kérdésekkel és azok megoldásával, amikor az Apache Ambari-ban Apache Hive hasznos adatokkal dolgozik.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hogyan exportáljon egy Hive-metaadattár, és importálja egy másik fürtre?

### <a name="resolution-steps"></a>A megoldás lépései

1. Kapcsolódjon a HDInsight-fürthöz egy Secure Shell-(SSH-) ügyfél használatával. További információ: [további olvasás](#additional-reading-end).

2. Futtassa a következő parancsot azon a HDInsight-fürtön, amelyről exportálni kívánja a metaadattár:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Ez a parancs létrehoz egy allatables. SQL nevű fájlt.

3. Másolja a alltables. SQL fájlt az új HDInsight-fürtre, majd futtassa a következő parancsot:

    ```apache
    hive -f alltables.sql
    ```

A megoldás lépéseiben szereplő kód azt feltételezi, hogy az új fürtön lévő adatelérési utak megegyeznek a régi fürtön lévő adatelérési utakkal. Ha az adatelérési utak eltérnek, manuálisan szerkesztheti a `alltables.sql` létrehozott fájlt, hogy az tükrözze a módosításokat.

### <a name="additional-reading"></a>További olvasnivaló

- [Kapcsolódás HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hogyan keresse meg a fürtön a kaptár-naplókat?

### <a name="resolution-steps"></a>A megoldás lépései

1. Kapcsolódjon a HDInsight-fürthöz az SSH használatával. További információ: **további olvasás**.

2. A kaptár-ügyfél naplófájljainak megtekintéséhez használja a következő parancsot:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Hive-metaadattár naplók megtekintéséhez használja a következő parancsot:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. A kaptár-kiszolgáló naplófájljainak megtekintéséhez használja a következő parancsot:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>További olvasnivaló

- [Kapcsolódás HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hogyan elindítja a kaptár-rendszerhéjt a fürt adott beállításaival?

### <a name="resolution-steps"></a>A megoldás lépései

1. A kaptár rendszerhéj indításakor a konfigurációs kulcs-érték párokat kell megadni. További információ: [további olvasás](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. A kaptár-rendszerhéj összes hatékony konfigurációjának listázásához használja a következő parancsot:

   ```apache
   hive> set;
   ```

   Például a következő paranccsal indítsa el a kaptár-rendszerhéjt a hibakeresési naplózással a konzolon:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>További olvasnivaló

- [Struktúra konfigurációjának tulajdonságai](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hogyan az Apache TEZ DAG-alapú adatelemzést a fürt kritikus elérési útjára?

### <a name="resolution-steps"></a>A megoldás lépései

1. Ha az Apache TEZ irányított aciklikus gráfot (DAG) szeretné elemezni egy fürtre kritikus gráfon, csatlakozzon a HDInsight-fürthöz SSH használatával. További információ: [további olvasás](#additional-reading-end).

2. A parancssorban futtassa a következő parancsot:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. A következő parancs használatával listázhatja a TEZ DAG elemzéséhez használható egyéb elemzőket:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Meg kell adnia egy példaként szolgáló programot az első argumentumként.

   A program érvényes nevei a következők:
    - **ContainerReuseAnalyzer**: tárolók ismételt újrafelhasználásának részletei egy Dag-ben
    - **CriticalPath**: a Dag kritikus elérési útját keresi
    - **LocalityAnalyzer**: a területi adatok kinyomtatása egy Dag-ben
    - **ShuffleTimeAnalyzer**: a megcsoszogott idő részleteinek elemzése egy Dag-ben
    - **SkewAnalyzer**: az elferdítés részleteinek elemzése egy Dag-ben
    - **SlowNodeAnalyzer**: csomópont adatainak nyomtatása Dag-ben
    - **SlowTaskIdentifier**: lassú feladat adatainak nyomtatása egy Dag-ben
    - **SlowestVertexAnalyzer**: a leglassabb csúcspont-Részletek nyomtatása egy Dag-ben
    - **SpillAnalyzer**: a nyomtatási kiömlés részletei egy Dag-ben
    - **TaskConcurrencyAnalyzer**: a feladat egyidejűségi adatainak nyomtatása egy Dag-ben
    - **VertexLevelCriticalPathAnalyzer**: megkeresi a kritikus elérési utat a vertex szinten egy Dag-ben

### <a name="additional-reading"></a>További olvasnivaló

- [Kapcsolódás HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hogyan letölti a TEZ DAG-adatait egy fürtről?

#### <a name="resolution-steps"></a>A megoldás lépései

Kétféle módon gyűjthetjük össze a TEZ DAG-adatokat:

- A parancssorból:

    Kapcsolódjon a HDInsight-fürthöz az SSH használatával. A parancssorban futtassa a következő parancsot:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Használja a Ambari TEZ nézetet:

  1. Nyissa meg a Ambari.
  2. Nyissa meg a TEZ nézetet (a jobb felső sarokban található csempék ikon alatt).
  3. Válassza ki a megtekinteni kívánt DAG-t.
  4. Válassza **az adatletöltés**lehetőséget.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>További olvasnivaló

[Kapcsolódás HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
