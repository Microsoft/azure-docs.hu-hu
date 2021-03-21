---
title: A hibaüzenet nem jelenik meg Apache Hive nézetben – Azure HDInsight
description: A lekérdezés Apache Hive nézetben meghiúsul az Azure HDInsight-fürt részleteinek megadása nélkül.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930997"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Forgatókönyv: a lekérdezési hibaüzenet nem jelenik meg Apache Hive nézetben az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Apache Hive View lekérdezési hibaüzenet a következőhöz hasonló módon fog kinézni, további információ nélkül:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Ok

Időnként előfordulhat, hogy a lekérdezési hiba hibaüzenete túl nagy a struktúra nézet főoldalán való megjelenítéshez.

## <a name="resolution"></a>Feloldás

Tekintse meg az értesítések lapot a Hive_view jobb felső sarkában, és tekintse meg a teljes stacktrace és-hibaüzenetet.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]