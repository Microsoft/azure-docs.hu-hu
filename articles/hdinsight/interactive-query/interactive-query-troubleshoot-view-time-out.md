---
title: Apache Hive megtekintési időtúllépés a lekérdezési eredményből – Azure HDInsight
description: Apache Hive megtekintési időtúllépés az Azure HDInsight lekérdezési eredményének beolvasása során
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930737"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive megtekintési időtúllépés az Azure HDInsight lekérdezési eredményének beolvasása során

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Bizonyos lekérdezések Apache Hive nézetből való futtatásakor a következő hiba fordulhat elő:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Ok

A kaptár nézet alapértelmezett időtúllépési értéke nem lehet megfelelő a futtatott lekérdezéshez. A megadott időtartam túl rövid ahhoz, hogy a struktúra nézet beolvassa a lekérdezés eredményét.

## <a name="resolution"></a>Feloldás

Növelje az Apache Ambari-struktúra nézet időtúllépéseit az alábbi tulajdonságok beállításával `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

A `HIVE_VIEW_INSTANCE_NAME` (z) értéke a kaptár nézet URL-címének végén érhető el.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]