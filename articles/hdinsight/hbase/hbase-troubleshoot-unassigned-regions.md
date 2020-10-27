---
title: A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight
description: A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 6225e51a7707a926df91e68fbb27bfb56f89a3ee
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539921"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>A régióbeli kiszolgálókkal kapcsolatos problémák az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-unassigned-regions"></a>Forgatókönyv: nem hozzárendelt régiók

### <a name="issue"></a>Probléma

A parancs futtatásakor a következőhöz `hbase hbck` hasonló hibaüzenet jelenik meg:

```
multiple regions being unassigned or holes in the chain of regions
```

Az Apache HBase Master felhasználói felületén megtekintheti az összes régió kiszolgálójának kiegyensúlyozatlan régiói számát. Ezután a parancs futtatásával `hbase hbck` megtekintheti a lyukakat a régió láncában.

### <a name="cause"></a>Ok

A lyukak az offline régiók eredményei lehetnek.

### <a name="resolution"></a>Feloldás

Javítsa ki a hozzárendeléseket. Az alábbi lépések végrehajtásával visszahelyezheti a hozzá nem rendelt régiókat a normál állapotba:

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.

1. Futtassa a `hbase zkcli` parancsot a ZooKeeper-rendszerhéjhoz való kapcsolódáshoz.

1. Futtatás `rmr /hbase/regions-in-transition` vagy `rmr /hbase-unsecure/regions-in-transition` parancs.

1. Lépjen ki a Zookeeper-rendszerhéjból a `exit` paranccsal.

1. Nyissa meg az Apache Ambari felhasználói felületét, majd indítsa újra az aktív HBase Master szolgáltatást.

1. Futtassa `hbase hbck` újra a parancsot (további beállítások nélkül). Ellenőrizze a kimenetet, és győződjön meg arról, hogy az összes régió hozzá van rendelve.

---

## <a name="scenario-dead-region-servers"></a>Forgatókönyv: Holt régió kiszolgálók

### <a name="issue"></a>Probléma

A régió-kiszolgálók nem indulnak el.

### <a name="cause"></a>Ok

Több felosztó WAL-könyvtár.

1. Aktuális WALs listájának beolvasása: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` .

1. Vizsgálja meg a `wals.out` fájlt. Ha túl sok felosztó könyvtár van (a *-felosztástól kezdve), akkor a régió-kiszolgáló valószínűleg sikertelen lesz a könyvtárak miatt.

### <a name="resolution"></a>Feloldás

1. A HBase leállítása a Ambari-portálról.

1. Végrehajtás `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` a WALs friss listájának lekéréséhez.

1. Helyezze át a *-felosztási könyvtárakat egy ideiglenes mappába, `splitWAL` és törölje a *-felosztási könyvtárakat.

1. Futtassa a `hbase zkcli` parancsot a Zookeeper-rendszerhéjhoz való kapcsolódáshoz.

1. Végrehajtás `rmr /hbase-unsecure/splitWAL` .

1. Indítsa újra a HBase szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.