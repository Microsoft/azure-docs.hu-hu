---
title: IllegalArgumentException-hiba Apache Spark – Azure HDInsight
description: IllegalArgumentException Apache Spark-tevékenységhez az Azure HDInsight for Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 334e8d129a7b5bb79c9e01d5ba7f347682e5bd79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545616"
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

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.