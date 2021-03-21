---
title: A fürtcsomópont elfogyott a lemezterület az Azure HDInsight
description: A fürt csomópontjának lemezterülettel kapcsolatos problémáinak Apache Hadoop elhárítása az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: c58ac1e65509299680f67cf17060ff3e9af4c817
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944358"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Forgatókönyv: a fürtcsomópont elfogyott a lemezterület az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy feladat meghiúsulhat a következőhöz hasonló hibaüzenettel: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Vagy a következőhöz hasonló Apache Ambari-riasztás jelenhet meg: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Ok

Lehetséges, hogy az Apache fonal-alkalmazás gyorsítótára felhasználta az összes rendelkezésre álló lemezterületet. A Spark-alkalmazás valószínűleg nem fog hatékonyan futni.

## <a name="resolution"></a>Feloldás

1. A Ambari felhasználói felületének használatával meghatározhatja, hogy melyik csomóponton fogy el a szabad lemezterület.

1. Határozza meg, hogy az aggasztó csomópont melyik mappája járul hozzá a lemezterület nagy részét. Először az SSH-t a csomópontra, majd futtassa a parancsot a `df` lemez használatának listázásához az összes csatlakoztatáshoz. Általában ez az `/mnt` OSS által használt Temp-lemez. Megadhat egy mappát, majd beírhatja egy `sudo du -hs` mappa összegzett fájlméretének megjelenítéséhez. Ha egy hasonló mappa jelenik meg `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , ez azt jelenti, hogy az alkalmazás továbbra is fut. Ennek oka lehet a RDD-megőrzés vagy a közbenső véletlenszerű fájlok.

1. A probléma megoldásához öld meg az alkalmazást, amely felszabadítja az alkalmazás által használt lemezterületet.

1. Ha a probléma gyakran előfordul a munkavégző csomópontokon, beállíthatja a szál helyi gyorsítótárának beállításait a fürtön.

    Nyissa meg a Ambari felhasználói felületét, és navigáljon a > konfigurációk--> Advanced elemre.  
    Adja hozzá az alábbi két tulajdonságot az egyéni yarn-site.xml szakaszhoz, és mentse a következőt:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Ha a fentiek nem javítják ki véglegesen a problémát, optimalizálja az alkalmazást.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]