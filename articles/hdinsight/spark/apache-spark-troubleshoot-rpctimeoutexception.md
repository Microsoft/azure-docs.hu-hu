---
title: RpcTimeoutException for Apache Spark takarékosság – Azure HDInsight
description: A nagyméretű adatkészletek Apache Spark takarékossági kiszolgáló használatával történő feldolgozásakor a 502-es hiba jelenik meg
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932711"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Forgatókönyv: RpcTimeoutException for Apache Spark takarékossági kiszolgáló az Azure HDInsight

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Spark-alkalmazás egy `org.apache.spark.rpc.RpcTimeoutException` kivétellel és egy üzenettel meghiúsul: `Futures timed out` az alábbi példában látható módon:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` és a `overhead limit exceeded` hibák a következő példában is szerepelhetnek `sparkthriftdriver.log` :

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Ok

Ezeket a hibákat az adatfeldolgozás során rendelkezésre álló memória-erőforrások hiánya okozza. Ha a Java-adatgyűjtési folyamat elindul, előfordulhat, hogy a Spark-alkalmazás nem válaszol. A lekérdezések megkezdik az időkorlátot, és leállítják a feldolgozást. A `Futures timed out` hiba súlyos terhelés alatt álló fürtöt jelez.

## <a name="resolution"></a>Feloldás

Növelje a fürt méretét több munkavégző csomópont hozzáadásával vagy a meglévő fürtcsomópontok memória-kapacitásának növelésével. Az adatfolyamatot úgy is beállíthatja, hogy csökkentse a egyszerre feldolgozott adatmennyiséget.

A `spark.network.timeout` meghatározza az összes hálózati kapcsolat időtúllépését. A hálózati időkorlát növelése több időt is igénybe vehet néhány kritikus művelet befejezésére, de ez nem oldja meg teljesen a problémát.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]