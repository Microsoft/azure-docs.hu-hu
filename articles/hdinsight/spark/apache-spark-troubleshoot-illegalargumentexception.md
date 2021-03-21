---
title: IllegalArgumentException-hiba Apache Spark – Azure HDInsight
description: IllegalArgumentException Apache Spark-tevékenységhez az Azure HDInsight for Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929401"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Forgatókönyv: Apache Spark tevékenység IllegalArgumentException az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő kivétel jelenik meg, amikor a Spark-tevékenységet egy Azure Data Factory-folyamatban próbálja végrehajtani:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Ok

A Spark-feladatok sikertelenek lesznek, ha az alkalmazás jar-fájlja nem a Spark-fürt alapértelmezett/elsődleges tárolójában található.

Ez egy ismert probléma a Spark nyílt forráskódú keretrendszerének a következő hibával: a Spark- [feladatok sikertelenek lesznek, ha az FS. defaultFS és az Application jar más URL-cím](https://issues.apache.org/jira/browse/SPARK-22587).

Ezt a problémát a Spark 2.3.0-ben oldottuk meg.

## <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy az alkalmazás jar a HDInsight-fürt alapértelmezett/elsődleges tárolójában van tárolva. Azure Data Factory esetén győződjön meg arról, hogy az ADF társított szolgáltatás a HDInsight alapértelmezett tárolóra mutat, nem pedig másodlagos tárolóként.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]