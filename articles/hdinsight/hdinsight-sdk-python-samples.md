---
title: 'Azure HDInsight: Python-minták'
description: Python-példák a GitHubon gyakori feladatokhoz a Pythonhoz készült HDInsight SDK használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 11/08/2019
ms.custom: devx-track-python
ms.openlocfilehash: f8b6f1f8b22435af33c9ae908bb0a3f6f3df8889
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535348"
---
# <a name="azure-hdinsight-python-samples"></a>Azure HDInsight: Python-minták

> [!div class="op_single_selector"]
> * [Python-példák](hdinsight-sdk-python-samples.md)
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Java-példák](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

> [!Important]
> A Python 2,7 január 2020 1-től lesz elavult. Ha továbbra is a Python 2,7-et használja, frissítsen a 3,7-re, hogy használhassa a HDInsight Python SDK-t.  

Ez a cikk a következő információkat tartalmazza:

* A fürtök létrehozására szolgáló feladatokhoz kapcsolódó mintákra mutató hivatkozások.
* Hivatkozások a más felügyeleti feladatokra vonatkozó hivatkozási tartalomra.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[Pythonhoz készült Azure HDInsight SDK](/python/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürt kezelése – létrehozás

* [Apache Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_kafka_cluster_sample.py)
* [Apache Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_spark_cluster_sample.py)
* [Apache Spark-fürt létrehozása Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_hadoop_cluster_with_adls_gen2_sample.py)
* [Enterprise Security Package (ESP) Apache Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_esp_cluster_sample.py)

Ezeket a mintákat a Pythonhoz a [hdinsight-Python-SDK-Samples](https://github.com/Azure-Samples/hdinsight-python-sdk-samples) GitHub-tárház klónozásával érheti el.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

A további SDK-funkciók kódrészletei a [HDINSIGHT SDK for Python](/python/api/overview/azure/hdinsight)dokumentációjában találhatók.