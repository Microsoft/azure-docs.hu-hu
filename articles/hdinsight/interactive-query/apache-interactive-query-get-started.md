---
title: Az Azure HDInsight interaktív lekérdezés használata
description: Útmutató a HDInsight interaktív lekérdezés (Hive LLAP) használata.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c1af99d64f9d9a7544d98ad34da5953fdda58222
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012877"
---
# <a name="use-interactive-query-with-hdinsight"></a>HDInsight interaktív lekérdezés használata
Interaktív lekérdezés (más néven a Hive LLAP vagy [alacsony késés Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) van egy Azure HDInsight [fürt típusa](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interaktív lekérdezés támogatja a memórián belüli gyorsítótárazáshoz, ami lehetővé teszi a Hive-lekérdezések gyorsabb és interaktívabb sokkal.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Az interaktív lekérdezési fürt különbözik egy Hadoop-fürtöt. Csak a Hive-szolgáltatást tartalmaz. 

> [!NOTE]
> A Hive-szolgáltatást, az az interaktív lekérdezési fürt csak az Ambari, Hive-nézet, Beeline és a Microsoft Hive adatforrásnevet illesztőprogram (Hive ODBC) keresztül érheti el. Nem férhet hozzá, a Hive konzolt, templeton eszközön keresztül végzett, a klasszikus Azure CLI vagy az Azure PowerShell használatával. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Az interaktív lekérdezési fürt létrehozása
A HDInsight-fürt létrehozásával kapcsolatos információkért lásd: [Hadoop-fürtök létrehozása a HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Válassza ki az interaktív lekérdezési fürtöt.

## <a name="execute-hive-queries-from-interactive-query"></a>Hive-lekérdezések végrehajtása az interaktív lekérdezés
Hive-lekérdezések végrehajtásához a következő lehetőségek állnak rendelkezésére:

* A Power BI használata

    Lásd: [adatok interaktív lekérdezéses Hive megjelenítése a Power bi használatával az Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) lásd [megjelenítése a Power bi használatával az Azure HDInsight a big Data típusú adatok](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* A Zeppelin használata

    Lásd: [a Zeppelin használata az Azure HDInsight Hive-lekérdezések futtatásához ](../hdinsight-connect-hive-zeppelin.md).

* A Visual Studio használata

    Lásd: [csatlakozhat az Azure HDInsight és a Hive-lekérdezések futtatásához a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* A Visual Studio kód használata

    Lásd: [Visual Studio Code használata a Hive, LLAP vagy pySpark](../hdinsight-for-vscode.md).
* Futtassa a Hive Ambari, Hive-nézet használatával.
  
    Lásd: [Hive-nézet használata Azure HDInsight a Hadoop-keretrendszerrel](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Futtassa a Hive a Beeline használatával.
  
    Lásd: [a Hive használata a hadooppal a HDInsight a Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    A Beeline az átjárócsomóponthoz, illetve üres élcsomópontot használhatja. Az üres élcsomópontot Beeline használatát javasoljuk. Egy HDInsight-fürt létrehozása az üres élcsomópontot használatával kapcsolatos információkért lásd: [üres élcsomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md).
* Futtassa a Hive Hive ODBC segítségével.
  
    Lásd: [Excel csatlakoztatása a Hadoophoz a Microsoft Hive ODBC-illesztő a](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

A Java adatbázis-kapcsolat (JDBC) kapcsolati karakterlánc megkeresése:

1. Jelentkezzen be az Ambari használatával a következő URL-címet: https://\<fürtnév\>. Azurehdinsight.NET formátumban van.
2. A bal oldali menüben válassza ki a **Hive**.
3. Másolja az URL-CÍMÉT, válassza a vágólapra ikont:
   
   ![A HDInsight Hadoop interaktív lekérdezés LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [HDInsight interaktív lekérdezési fürtökhöz létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).
* Ismerje meg, hogyan [megjelenítése a Power bi használatával az Azure HDInsight a big Data típusú adatok](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Ismerje meg, hogyan [az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használatával ](../hdinsight-connect-hive-zeppelin.md).
* Ismerje meg, hogyan [a Data Lake Tools for Visual Studio használatával Hive-lekérdezések futtatása](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Ismerje meg, hogyan [HDInsight Tools for Visual Studio Code használata](../hdinsight-for-vscode.md).
* Ismerje meg, hogyan [Hive-nézet használata a HDInsight Hadoop-keretrendszerrel](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Ismerje meg, hogyan [a Beeline használata a HDInsight Hive-lekérdezések elküldéséhez](../hadoop/apache-hadoop-use-hive-beeline.md).
* Ismerje meg, hogyan [Excel összekapcsolása a Hadooppal a Microsoft Hive ODBC-illesztő a](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

