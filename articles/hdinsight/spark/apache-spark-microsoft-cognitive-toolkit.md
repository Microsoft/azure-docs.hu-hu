---
title: Microsoft Cognitive Toolkit Apache Spark-Azure HDInsight
description: Megtudhatja, hogyan alkalmazhat egy betanított Microsoft Cognitive Toolkit mély tanulási modellt a Spark Python API-val egy Azure HDInsight Spark-fürtben.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: cddbc4b6a5c7a2c787c8305fdf703e34543746f8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929970"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Microsoft Cognitive Toolkit Deep learning-modell használata Azure HDInsight Spark-fürttel

Ebben a cikkben a következő lépéseket hajtja végre.

1. Futtasson egyéni parancsfájlt [Microsoft Cognitive Toolkit](/cognitive-toolkit/) telepítéséhez Azure HDInsight Spark-fürtön.

2. Töltsön fel egy [Jupyter Notebookt](https://jupyter.org/) a [Apache Spark](https://spark.apache.org/) -fürtbe, és Ismerje meg, hogyan alkalmazhat egy betanított Microsoft Cognitive Toolkit mély tanulási modellt egy Azure Blob Storage-fiókban lévő fájlokra a [Spark Python API (PySpark) használatával.](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása](./apache-spark-jupyter-spark-sql-use-portal.md).

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark a HDInsight-on](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Hogyan működik ez a megoldás?

Ez a megoldás a jelen cikk és a jelen cikk részeként feltöltött Jupyter Notebook között oszlik meg. Ebben a cikkben a következő lépéseket hajtja végre:

* Parancsfájl-művelet futtatása HDInsight Spark-fürtön Microsoft Cognitive Toolkit és Python-csomagok telepítéséhez.
* Töltse fel a megoldást futtató Jupyter Notebook a HDInsight Spark-fürtre.

A következő fennmaradó lépéseket a Jupyter Notebook tárgyalja.

* Betöltheti a mintaképeket egy Spark rugalmas elosztott adatkészletbe vagy RDD.
  * Modulok betöltése és beállításkészlet megadása
  * Töltse le az adatkészletet helyileg a Spark-fürtön.
  * Alakítsa át az adatkészletet egy RDD.
* A képeket a betanított Cognitive Toolkit modell használatával szerzi be.
  * Töltse le a betanított Cognitive Toolkit modellt a Spark-fürtre.
  * A munkavégző csomópontok által használandó függvények definiálása.
  * A lemezképek kiértékelése a munkavégző csomópontokon.
  * Modell pontosságának kiértékelése.

## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit telepítése

A Microsoft Cognitive Toolkitt a Spark-fürtön parancsfájl-művelettel telepítheti. A parancsfájl-művelet egyéni parancsfájlokat használ a fürtön olyan összetevők telepítéséhez, amelyek alapértelmezés szerint nem érhetők el. Az egyéni szkriptet használhatja a Azure Portal a HDInsight .NET SDK vagy a Azure PowerShell használatával. Az eszközkészletet a fürt létrehozása vagy a fürt üzembe helyezése után is telepítheti a parancsfájl segítségével.

Ebben a cikkben a portál használatával telepítjük az eszközkészletet a fürt létrehozása után. Az egyéni szkript futtatásának egyéb módjaival kapcsolatban lásd: [HDInsight-fürtök testreszabása parancsfájl-művelet használatával](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Az Azure Portal parancsfájl-művelet futtatására való használatával kapcsolatos utasításokért lásd: [HDInsight-fürtök testreszabása parancsfájl-művelettel](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Győződjön meg arról, hogy a következő adatokat adja meg a Microsoft Cognitive Toolkit telepítéséhez. Használja a következő értékeket a parancsfájl művelethez:

|Tulajdonság |Érték |
|---|---|
|Parancsfájl típusa|– Egyéni|
|Name| A MCT telepítése|
|Bash-parancsfájl URI-ja|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Csomópont típusa (i):|Head, Worker|
|Paraméterek|Nincs|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>A Jupyter Notebook feltöltése Azure HDInsight Spark-fürtre

Ahhoz, hogy a Microsoft Cognitive Toolkitt a Azure HDInsight Spark fürthöz használhassa, be kell töltenie a Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough. ipynb** a Azure HDInsight Spark-fürtre. Ez a jegyzetfüzet a GitHubon érhető el [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Töltse le és csomagolja ki [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/jupyter` , ahol a a `CLUSTERNAME` fürt neve.

1. A Jupyter Notebook válassza a jobb felső sarokban található **feltöltés** lehetőséget, majd navigáljon a letöltés és a fájl kiválasztása elemre `CNTK_model_scoring_on_Spark_walkthrough.ipynb` .

    ![Jupyter Notebook feltöltése Azure HDInsight Spark-fürtre](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter Notebook feltöltése Azure HDInsight Spark-fürtre")

1. Válassza a **feltöltés** újra lehetőséget.

1. A jegyzetfüzet feltöltése után kattintson a jegyzetfüzet nevére, majd kövesse a jegyzetfüzetben megjelenő utasításokat az adathalmaz betöltéséhez és a cikk végrehajtásához.

## <a name="see-also"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetria-adatelemzési szolgáltatásának használata a HDInsight-ben Apache Spark](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
