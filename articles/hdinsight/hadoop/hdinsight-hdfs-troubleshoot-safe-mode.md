---
title: Az Azure HDInsight-fürtön biztonságos módban ragadt helyi HDFS
description: A helyi Apache HDFS beragadása csökkentett módban az Apache-fürtön az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944442"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Forgatókönyv: az Azure HDInsight-fürtön biztonságos módban ragadt helyi HDFS

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A helyi Apache Hadoop elosztott fájlrendszer (HDFS) biztonságos módban ragadt a HDInsight-fürtön. A következőhöz hasonló hibaüzenet jelenik meg:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Ok

A HDInsight-fürtöt az alábbi néhány csomópontra csökkentették, vagy a csomópontok száma közel van a HDFS replikálási tényezőhöz.

## <a name="resolution"></a>Feloldás

1. Jelentés a HDInsight-fürt HDFS állapotáról a következő paranccsal:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. A HDInsight-fürtön található HDFS integritásának ellenőrzése a következő paranccsal:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Ha megállapítják, hogy nincsenek hiányzó, sérült vagy replikált blokkok, vagy ezek a blokkok figyelmen kívül hagyhatók, futtassa a következő parancsot, hogy a név csomóponton kívülre kerüljön a biztonságos módból:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]