---
title: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
description: Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929212"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper-kiszolgáló nem tud kvórumot alkotni az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtök Zookeeperek kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

* Az erőforrás-kezelők készenléti módba is léphetnek
* A Namenodes készenléti állapotban vannak
* A Spark, a kaptár és a fonalas feladatok, illetve a kaptár-lekérdezések meghiúsulnak a Zookeeper-kapcsolatok meghibásodása miatt
* A LLAP démonok nem indulnak el biztonságos Spark-vagy biztonságos interaktív kaptár-fürtökön

## <a name="sample-log"></a>Minta napló

A következőhöz hasonló hibaüzenet jelenhet meg a fonalak naplóiban (/var/log/Hadoop-yarn/yarn/yarn-yarn *. log a átjárócsomópontokkal):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Kapcsolódó problémák

* A magas rendelkezésre állású szolgáltatások, például a fonalak, a NameNode és a Livy számos okból letérhetnek.
* Erősítse meg az Zookeeper-kapcsolatokhoz kapcsolódó naplókat
* Győződjön meg arról, hogy a probléma ismételten megtörténik (ne használja ezeket a megoldásokat egyszeri esetekre)
* A feladatok a Zookeeper kapcsolódási problémái miatt átmenetileg sikertelenek lehetnek

## <a name="common-causes-for-zookeeper-failure"></a>Gyakori okok a Zookeeper meghibásodása esetén

* Magas CPU-használat a Zookeeper-kiszolgálókon
  * Ha a Ambari felhasználói felületén a Zookeeper-kiszolgálókon közel 100%-os tartós CPU-használat látható, akkor a Zookeeper-munkamenetek az adott idő alatt megnyitva lejárnak, és időtúllépést okozhatnak.
* A Zookeeper-ügyfelek gyakori időtúllépéseket jelentettek
  * A Resource Manager, a Namenode és más naplók naplóiban gyakori ügyfélkapcsolati időtúllépések jelennek meg
  * Ez a kvórum elvesztését, a gyakori feladatátvételeket és egyéb problémákat eredményezhet

## <a name="check-for-zookeeper-status"></a>Zookeeper állapotának keresése

* A Zookeeper-kiszolgálók megkeresése a/etc/hosts fájlból vagy a Ambari felhasználói felületéről
* Futtassa a következő parancsot:
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (vagy 2182)  
  * A 2181-es port az Apache Zookeeper-példány
  * A HDInsight Zookeeper a 2182-es portot használja (ha olyan szolgáltatásokat kíván biztosítani, amelyek nem natív módon vannak megadva)
  * Ha a parancs nem jelenít meg kimenetet, akkor az azt jelenti, hogy a Zookeeper-kiszolgálók nem futnak.
  * Ha a kiszolgálók futnak, az eredmény tartalmazni fogja az ügyfélkapcsolatok és egyéb statisztikák statikaét

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>CPU-terhelési csúcsok óránként

* Jelentkezzen be a Zookeeper-kiszolgálóra, és keresse meg a/etc/crontab
* Ha jelenleg bármilyen óránkénti feladat fut, akkor a különböző Zookeeper-kiszolgálók közötti kezdési idő véletlenszerű.
  
## <a name="purging-old-snapshots"></a>Régi Pillanatképek törlése

* A zookeeperek a régi Pillanatképek automatikus törlésére vannak konfigurálva
* Alapértelmezés szerint az utolsó 30 pillanatkép megmarad
* A megőrzött Pillanatképek számát a konfigurációs kulcs vezérli `autopurge.snapRetainCount` . Ez a tulajdonság a következő fájlokban található:
  * `/etc/zookeeper/conf/zoo.cfg` Hadoop Zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` HDInsight Zookeeper
* Állítsa `autopurge.snapRetainCount` 3 értékre, majd indítsa újra a Zookeeper-kiszolgálókat.
  * A Hadoop Zookeeper config frissíthető, és a szolgáltatás a Ambari-n keresztül indítható újra.
  * A HDInsight Zookeeper manuális leállítása és újraindítása
    * `sudo lsof -i :2182` Megadja a folyamat AZONOSÍTÓját a kill művelethez
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Ne törölje manuálisan a pillanatképek törlését – a pillanatképek manuális törlése adatvesztést eredményezhet.

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>A Zookeeper-kiszolgáló CancelledKeyException nem szükséges a pillanatképek karbantartása

* Ez a kivétel a Zookeeper-kiszolgálókon (/var/log/Zookeeper/Zookeeper-Zookeeper-* vagy/var/log/hdinsight-Zookeeper/Zookeeper * fájlok) látható.
* Ez a kivétel általában azt jelenti, hogy az ügyfél már nem aktív, és a kiszolgáló nem tud üzenetet küldeni
* Ez a kivétel azt is jelzi, hogy a Zookeeper-ügyfél idő előtt véget ér
* Keresse meg a dokumentumban ismertetett egyéb tüneteket

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).
- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.
- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.