---
title: 'Azure HDInsight: Java-példák'
description: Java-példák a Githubon található a HDInsight SDK használata Javához készült gyakori feladatok.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 90723afd8864bd7ec64f7b2e81e11644561cb52d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763449"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight: Java-példák

> [!div class="op_single_selector"]
> * [Java-példák](hdinsight-sdk-java-samples.md)
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Python-példák](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* Szereplő hivatkozások a fürt létrehozási feladatok.
* Segédanyagok a többi felügyeleti feladat mutató hivatkozásokat tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [A Javához készült Azure HDInsight SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürtkezelés - létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [Spark-fürt létrehozása az Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [Spark-fürt létrehozása a vállalati biztonsági csomag (ESP)](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

Klónozásával Java ezeket a mintákat is kap a [hdinsight-java-sdk-minták](https://github.com/Azure-Samples/hdinsight-java-sdk-samples) GitHub-adattárban.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Kódrészletek a tekintheti meg a további SDK-funkciók esetében a [HDInsight SDK for Java dokumentáció](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-preview).
