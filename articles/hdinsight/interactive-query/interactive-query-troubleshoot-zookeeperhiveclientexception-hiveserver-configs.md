---
title: Apache Hive Zeppelin-értelmező hibája – Azure HDInsight
description: Az Apache Zeppelin-struktúra JDBC-értelmező nem megfelelő URL-címre mutat az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930752"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive Zeppelin-értelmező Zookeeper hibát ad az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Hive LLAP-fürtön a Zeppelin-értelmező a következő hibaüzenetet adja meg a lekérdezés végrehajtásának megkísérlése során:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Ok

A Zeppelin-struktúra JDBC-értelmező nem megfelelő URL-címre mutat.

## <a name="resolution"></a>Feloldás

1. Navigáljon a kaptár összetevő összegzéséhez, és másolja a "kaptár JDBC URL-címét" a vágólapra.

1. Navigáljon ide: `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. A JDBC beállításainak szerkesztése: frissítse a kaptár. URL értékét a kaptár JDBC URL-címére, amelyet az 1. lépésben másolt.

1. Mentse, majd próbálkozzon újra a lekérdezéssel

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]