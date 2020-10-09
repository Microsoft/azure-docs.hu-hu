---
title: 'Azure HDInsight: .NET-minták'
description: C# .NET-példák keresése a GitHubon gyakori feladatokhoz a .NET-hez készült HDInsight SDK használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: 097323b4566daeda737428121bee4d64e9f7248b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "74951564"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: .NET-minták

> [!div class="op_single_selector"]
> * [.NET-példák](hdinsight-sdk-dotnet-samples.md)
> * [Python-példák](hdinsight-sdk-python-samples.md)
> * [Java-példák](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ez a cikk a következő információkat tartalmazza:

* A fürtök létrehozására szolgáló feladatokhoz kapcsolódó mintákra mutató hivatkozások.
* Hivatkozások a más felügyeleti feladatokra vonatkozó hivatkozási tartalomra.

[Aktiválhatja Visual Studio-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A Visual Studio-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>Előfeltétel

[.NET-hez készült Azure HDInsight SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Fürt kezelése – létrehozás

* [Kafka-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Spark-fürt létrehozása](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Spark-fürt létrehozása Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Spark-fürt létrehozása Enterprise Security Package (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Ezeket a mintákat a .NET-hez a [hdinsight-DotNet-SDK-Samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub-tárház klónozásával érheti el.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Ehhez a további SDK-funkciókhoz tartozó kódrészletek a [HDINSIGHT SDK for .net dokumentációjában](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)találhatók.
