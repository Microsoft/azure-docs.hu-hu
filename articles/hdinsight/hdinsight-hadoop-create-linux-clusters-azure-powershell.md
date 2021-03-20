---
title: Apache Hadoop-fürtök létrehozása a PowerShell használatával – Azure HDInsight
description: Megtudhatja, hogyan hozhat létre Apache Hadoop-, Apache-HBase-, Apache Storm-vagy Apache Spark-fürtöket Linux rendszeren a HDInsight Azure PowerShell használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 86622bf96d4b59537a2946073fdc638e51c3852d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945835"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Linux-alapú fürtök létrehozása a HDInsight-ben a Azure PowerShell használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A Azure PowerShell egy hatékony parancsfájlkezelési környezet, amellyel szabályozhatja és automatizálhatja a számítási feladatok üzembe helyezését és kezelését Microsoft Azureban. Ebből a dokumentumból megtudhatja, hogyan hozhat létre egy Linux-alapú HDInsight-fürtöt Azure PowerShell használatával. Egy példa parancsfájlt is tartalmaz.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Az modul.

## <a name="create-cluster"></a>Fürt létrehozása

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ha Azure PowerShell használatával szeretne létrehozni egy HDInsight-fürtöt, el kell végeznie az alábbi eljárásokat:

* Azure-erőforráscsoport létrehozása
* Azure Storage-fiók létrehozása
* Azure Blob-tároló létrehozása
* HDInsight-fürt létrehozása

> [!NOTE]
> A PowerShell használatával HDInsight-fürtöt hozhat létre a Azure Data Lake Storage Gen2val. jelenleg nem támogatott.

Az alábbi szkript bemutatja, hogyan hozhat létre új fürtöt:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

A fürt bejelentkezési Hadoop megadott értékeket a rendszer a fürthöz tartozó felhasználói fiók létrehozásához használja. Ezzel a fiókkal csatlakozhat a fürtben üzemeltetett szolgáltatásokhoz (például webes felület vagy REST API-k).

Az SSH-felhasználó számára megadott értékek a fürt SSH-felhasználójának létrehozására szolgálnak. Ezzel a fiókkal elindíthat egy távoli SSH-munkamenetet a fürtön, és futtathatja a feladatokat. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Ha több mint 32 feldolgozó csomópontot kíván használni (a fürt létrehozásakor vagy a fürt a létrehozás után történő skálázásával), akkor a fő csomópontok méretét is meg kell adnia legalább 8 maggal és 14 GB RAM-mal.
>
> További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

Egy fürt létrehozása akár 20 percet is igénybe vehet.

## <a name="create-cluster-configuration-object"></a>Fürt létrehozása: konfigurációs objektum

HDInsight-konfigurációs objektumot a parancsmag használatával is létrehozhat [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) . Ezt a konfigurációs objektumot azután módosíthatja, hogy a fürt további konfigurációs beállításait is engedélyezze. Végül használja a `-Config` parancsmag paraméterét a [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster) konfiguráció használatához.

A következő parancsfájl egy konfigurációs objektumot hoz létre egy R Server HDInsight-fürtön való konfigurálásához. A konfiguráció lehetővé teszi egy peremhálózati csomópont, egy RStudio és egy további Storage-fiók használatát.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen lévő Storage-fiók használata nem támogatott. Ha ezt a példát használja, hozza létre a további Storage-fiókot a-kiszolgálóval megegyező helyen.

## <a name="customize-clusters"></a>Fürtök testreszabása

* Lásd: [HDInsight-fürtök testreszabása a bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Lásd: [HDInsight-fürtök testreszabása parancsfájl-művelet használatával](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, az alábbi források segítségével megismerheti, hogyan dolgozhat a fürttel.

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-fürtök

* [Java-topológiák fejlesztése a HDInsight-alapú Storm számára](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a Storm on HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topológia üzembe helyezése és figyelése a Storm on HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Fürtök Apache Spark

* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](spark/apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)