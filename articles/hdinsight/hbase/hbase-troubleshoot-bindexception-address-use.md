---
title: BindException – az Azure HDInsight már használatban van a címe
description: BindException – az Azure HDInsight már használatban van a címe
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: c0a7bc7629b5c2a2e458ba94d62e341f578fdd25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946399"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Forgatókönyv: BindException – már használatban van az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase-régió kiszolgálójának újraindítási művelete nem fejeződött be. A (z) azon `region-server.log` `/var/log/hbase` munkavégző csomópontok mappájában, ahol a régió kiszolgálójának indítása sikertelen, a következőhöz hasonló hibaüzenet jelenhet meg:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Ok

Az Apache HBase-régió kiszolgálóinak újraindítása a munkaterhelés-tevékenységek során. Az alábbiakban látható, hogy mi történik a jelenetek mögött, amikor egy felhasználó elindítja az újraindítási műveletet a HBase-régió kiszolgálójának Apache Ambari felhasználói felületéről:

1. A Ambari ügynök leállítási kérelmet küld a régió-kiszolgálónak.

1. A Ambari-ügynök 30 másodpercig várakozik, hogy a régió kiszolgálója szabályosan leálljon

1. Ha az alkalmazás továbbra is kapcsolódik a régió-kiszolgálóhoz, a kiszolgáló nem fog azonnal leállni. A 30 másodperces időtúllépés lejár a leállítás előtt.

1. 30 másodperc elteltével a Ambari-ügynök kényszerített kill ( `kill -9` ) parancsot küld a régió-kiszolgálónak.

1. A hirtelen leállás miatt, bár a régió-kiszolgáló folyamata leállt, előfordulhat, hogy a folyamathoz társított port nem jelenik meg, ami végül a következőhöz vezet: `AddressBindException` .

## <a name="resolution"></a>Feloldás

Az újraindítás megkezdése előtt csökkentse a HBase-régió kiszolgálóinak terhelését. Emellett érdemes kiüríteni az összes táblát. A táblák kiürítésével kapcsolatban lásd [: HDInsight HBase: az Apache HBase-fürt újraindítási idejének fejlesztése a táblák kiürítésével](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Azt is megteheti, hogy manuálisan szeretné újraindítani a régió-kiszolgálókat a munkavégző csomópontokon a következő parancsok használatával:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.