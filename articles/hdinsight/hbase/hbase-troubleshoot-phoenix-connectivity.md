---
title: Kapcsolódási problémák Apache Phoenix az Azure HDInsight
description: Kapcsolódási problémák az Apache HBase és a Apache Phoenix között az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 2cc6556f681ece170bdfe02b985f56274c0faa1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936961"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Forgatókönyv: Apache Phoenix kapcsolódási problémák az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet csatlakozni az Apache HBase a Apache Phoenix használatával. Az okok eltérőek lehetnek.

## <a name="cause-incorrect-ip"></a>Ok: helytelen az IP-cím

Az aktív Zookeeper csomópontjának helytelen IP-címe.

### <a name="resolution"></a>Feloldás

Az aktív Zookeeper csomópontjának IP-címe a Ambari felhasználói felületén azonosítható a **HBase**  >  **Quick Links**  >  **ZK (aktív)**  >  **Zookeeper-információ** hivatkozásait követve. Szükség szerint javítsa ki az IP-címet.

---

## <a name="cause-systemcatalog-table-offline"></a>Ok: a SYSTEM. Katalógus tábla kapcsolat nélkül

A parancs futtatásakor a `!tables` következőhöz hasonló hibaüzenet jelenik meg:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

A parancs futtatásakor a `count 'SYSTEM.CATALOG'` következőhöz hasonló hibaüzenet jelenik meg:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Feloldás

Az Apache Ambari felhasználói felületén hajtsa végre a következő lépéseket a HMaster szolgáltatás újraindításához az összes ZooKeeper-csomóponton:

1. A HBase **Összefoglalás** szakaszából válassza a **HBase**  >  **aktív HBase Master** elemet.

1. Az **összetevők** szakaszban indítsa újra a HBase Master szolgáltatást.

1. Ismételje meg ezeket a lépéseket az összes fennmaradó **készenléti HBase Master** szolgáltatás esetében.

Akár öt percet is igénybe vehet, amíg a HBase Master szolgáltatás stabilizálni és befejezni a helyreállítást. Miután a `SYSTEM.CATALOG` tábla visszatér a normális kerékvágásba, a Apache Phoenix kapcsolódási problémáját automatikusan meg kell oldani.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.