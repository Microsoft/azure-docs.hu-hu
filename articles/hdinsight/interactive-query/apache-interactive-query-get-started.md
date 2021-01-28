---
title: Mi az az interaktív lekérdezés az Azure HDInsight?
description: Bevezetés az interaktív lekérdezésbe, más néven Apache Hive LLAP, az Azure HDInsight
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 2813554700e015c0ac34e47d632d16d97c948c4e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941086"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Mi az az interaktív lekérdezés az Azure HDInsight-ben?

Az interaktív lekérdezés (más néven Apache Hive LLAP vagy [alacsony késésű analitikai feldolgozás](https://cwiki.apache.org/confluence/display/Hive/LLAP)) egy Azure HDInsight- [fürt típusú](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type). Az interaktív lekérdezés támogatja a memóriabeli gyorsítótárazást, ami gyorsabb és sokkal több interaktív Apache Hive-lekérdezéseket tesz lehetővé. Az ügyfelek interaktív lekérdezéssel kérdezik le az Azure Storage & Azure Data Lake Storageban tárolt adatlekérdezéseket rendkívül gyors módon. Az interaktív lekérdezés megkönnyíti a fejlesztők és az adatfejlesztők számára, hogy a leginkább kedvelt BI-eszközökkel működjenek együtt a big data. A HDInsight interaktív lekérdezése több eszközt is támogat, amelyekkel könnyen elérhetővé big data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Egy interaktív lekérdezési fürt különbözik egy Apache Hadoop fürttől. Csak a kaptár szolgáltatást tartalmazza.

Az interaktív lekérdezési fürtben csak az Apache Ambari kaptár View, a Beeline és a Microsoft kaptár Open Database Connectivity Driver (kaptár ODBC) használatával férhet hozzá a kaptár szolgáltatáshoz. Nem fér hozzá a kaptár-konzolon, a Templeton, a klasszikus Azure CLI-n vagy a Azure PowerShellon keresztül.

## <a name="create-an-interactive-query-cluster"></a>Interaktív lekérdezési fürt létrehozása

További információ a HDInsight-fürtök létrehozásáról: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md). Válassza ki az interaktív lekérdezési fürt típusát.

> [!IMPORTANT]
> Az interaktív lekérdezési fürtök minimális átjárócsomóponthoz mérete Standard_D13_v2. További információért tekintse meg az [Azure virtuális gép méretezése diagramot](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive lekérdezések végrehajtása interaktív lekérdezésből

A kaptár-lekérdezések végrehajtásához a következő lehetőségek állnak rendelkezésre:

|Metódus |Leírás |
|---|---|
|Microsoft Power BI|Tekintse meg az [interaktív lekérdezési Apache Hive az Azure-HDInsight Power BIával](./apache-hadoop-connect-hive-power-bi-directquery.md), valamint az [azure-HDInsight Power BIával kapcsolatos Big Data megjelenítését](../hadoop/apache-hadoop-connect-hive-power-bi.md)ismertető témakört.|
|Visual Studio|Lásd: [Kapcsolódás az Azure HDInsight és Apache Hive lekérdezések futtatása a Visual studióhoz készült Data Lake Tools használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio Code|Lásd: [a Visual Studio Code használata Apache Hive, LLAP vagy pySpark](../hdinsight-for-vscode.md).|
|Apache Ambari-struktúra nézet|Lásd: [Apache Hive nézet használata az Azure HDInsight Apache Hadoopával](../hadoop/apache-hadoop-use-hive-ambari-view.md). A kaptár nézet nem érhető el a HDInsight 4,0 esetében.|
|Apache Beeline|Lásd: a [Apache Hive használata a HDInsight-ben való Apache Hadoop a Beeline paranccsal](../hadoop/apache-hadoop-use-hive-beeline.md). A Beeline a fő csomópontból vagy egy üres peremhálózati csomópontból is használható. Azt javasoljuk, hogy a Beeline üres peremhálózati csomópontról legyen használatban. A HDInsight-fürtök üres peremhálózati csomóponttal való létrehozásával kapcsolatos információkért lásd: [üres peremhálózati csomópontok használata a HDInsight-ben](../hdinsight-apps-use-edge-node.md).|
|Struktúra ODBC|Lásd: [az Excel Összekapcsolásának Apache Hadoop a Microsoft kaptár ODBC-illesztővel](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

A Java Database Connectivity (JDBC) kapcsolati karakterláncának megkeresése:

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` , ahol a a `CLUSTERNAME` fürt neve.
1. Az URL-cím másolásához válassza a vágólap ikont:

   ![HDInsight Hadoop interaktív lekérdezési LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre interaktív lekérdezési fürtöket a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md).
* Ismerje meg, hogyan [jelenítheti meg Big dataeit az Azure HDInsight Power BIával](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Ismerje meg, hogyan [futtathat Apache Hive-lekérdezéseket az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
