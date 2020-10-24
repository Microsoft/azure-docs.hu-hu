---
title: 'Azure HDInsight: Java-minták'
description: Java-példák a GitHubon gyakori feladatokhoz a Javához készült HDInsight SDK használatával.
author: hrasheed-msft
ms.custom: devx-track-java
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 11/29/2019
ms.openlocfilehash: 2698e88517642460d756ca7c57031a8f1912358f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490934"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight: Java-minták

> [!div class="op_single_selector"]
> * [Java-példák](hdinsight-sdk-java-samples.md)
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Python-példák](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* A fürtök létrehozására szolgáló feladatokhoz kapcsolódó mintákra mutató hivatkozások.
* Hivatkozások a más felügyeleti feladatokra vonatkozó hivatkozási tartalomra.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[Javához készült Azure HDInsight SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürt kezelése – létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [Spark-fürt létrehozása Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [Spark-fürt létrehozása Enterprise Security Package (ESP)](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

Ezeket a mintákat a Javához a [hdinsight-Java-SDK-Samples](https://github.com/Azure-Samples/hdinsight-java-sdk-samples) GitHub-tárház klónozásával érheti el.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Ehhez a további SDK-funkciókhoz tartozó kódrészletek a [HDINSIGHT SDK for Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight)dokumentációjában találhatók.
