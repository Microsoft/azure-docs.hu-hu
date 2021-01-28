---
title: Apache Hive kapcsolatok az Apache Zookeeper – Azure HDInsight
description: A Apache Hive az Apache Zookeeper-problémák miatt nem érhető el az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939307"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive nem tud kapcsolatot létesíteni az Apache Zookeeper az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A kaptár nézet nem érhető el, és a naplók a `/var/log/hive` következőhöz hasonló hibaüzenetet jelenítenek meg:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Ok

Előfordulhat, hogy a kaptár nem tud kapcsolatot létesíteni a Zookeeper-vel, ami megakadályozza a kaptár nézet indítását.

## <a name="resolution"></a>Feloldás

1. Győződjön meg arról, hogy a Zookeeper szolgáltatás kifogástalan állapotban van.

1. Ellenőrizze, hogy a Zookeeper szolgáltatás rendelkezik-e ZNode-bejegyzéssel a kaptár Kiszolgáló2. Az érték hiányzik vagy helytelen.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. A kapcsolat újbóli létrehozásához indítsa újra a Zookeeper-csomópontokat, és indítsa újra a HiveServer2.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]