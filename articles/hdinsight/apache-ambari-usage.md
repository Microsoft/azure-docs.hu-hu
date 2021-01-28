---
title: Apache Ambari-használat az Azure HDInsight
description: Megtudhatja, hogyan használja az Apache Ambari az Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2021
ms.openlocfilehash: 6233c42d7fbf8dc7821d26f77171c44485fb8d34
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946945"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-használat az Azure HDInsight

A HDInsight az Apache Ambari-t használja a fürtök üzembe helyezéséhez és felügyeletéhez. A Ambari-ügynökök minden csomóponton futnak (átjárócsomóponthoz, Worker node, Zookeeper és élcsomópontok, ha létezik). A Ambari-kiszolgáló csak a átjárócsomóponthoz fut (hn0 vagy hn1). Egyszerre csak egy példányban fut a Ambari-kiszolgáló. Ezt a HDInsight feladatátvételi vezérlő vezérli. Ha az egyik átjárócsomópontokkal újraindításra vagy karbantartásra van lebontva, a másik átjárócsomóponthoz lesz aktív, és a második átjárócsomóponthoz a Ambari-kiszolgáló lesz elindítva.

Az összes fürtkonfiguráció a [Ambari felhasználói felületén](./hdinsight-hadoop-manage-ambari.md)keresztül történik, a csomópont újraindításakor a rendszer felülírja a helyi módosításokat.

## <a name="failover-controller-services"></a>Feladatátvételi vezérlő szolgáltatásai

A HDInsight feladatátvételi vezérlő a átjárócsomóponthoz gazdagép IP-címének frissítéséhez is felelős, amely az aktuális aktív fő csomópontra mutat. Az összes Ambari-ügynök úgy van konfigurálva, hogy az állapotát és szívverését a átjárócsomóponthoz gazdagépnek jelentse. A feladatátvételi vezérlő a fürt minden csomópontján futó szolgáltatások készlete, ha nem futnak, előfordulhat, hogy a átjárócsomóponthoz feladatátvétel nem működik megfelelően, és a HTTP 502-es verzióval próbálkozik a Ambari-kiszolgáló elérésére.

A átjárócsomóponthoz aktív állapotának vizsgálatához az egyik módszer az SSH használata a fürt egyik csomópontján, majd az `ping headnodehost` IP-cím és a két átjárócsomópontokkal összevetése.

Ha a feladatátvételi vezérlő szolgáltatásai nem futnak, előfordulhat, hogy a átjárócsomóponthoz feladatátvétel nem fog megfelelően működni, ami végül nem fut a Ambari-kiszolgálóval. A feladatátvételi vezérlő szolgáltatásainak futtatásához futtassa a következőt:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Naplók

Az aktív átjárócsomóponthoz a Ambari-kiszolgáló naplóit a következő helyen tekintheti meg:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

A fürt bármely csomópontján megtekintheti a Ambari-ügynök naplóit a következő helyen:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Szolgáltatás indítási lépései

A szolgáltatás indítási ideje a rendszerindításkor:

1. A Hdinsight-Agent elindítja a feladatátvételi vezérlő szolgáltatásait.
1. A feladatátvételi vezérlő szolgáltatás minden csomóponton és Ambari-kiszolgálón elindítja a Ambari-ügynököt az aktív átjárócsomóponthoz.

## <a name="ambari-database"></a>Ambari-adatbázis

A HDInsight létrehoz egy adatbázist SQL Database a motorháztető alatt, hogy az adatbázisként szolgáljon a Ambari-kiszolgálóhoz. Az alapértelmezett [szolgáltatási szintet a S0](../azure-sql/database/elastic-pool-scale.md).

Ha a fürt létrehozásakor a munkavégző csomópontok száma nagyobb, mint 16, az S2 az adatbázis-szolgáltatási réteg.

## <a name="takeaway-points"></a>Elvihető pontok

Soha ne indítsa el vagy állítsa le a ambari-kiszolgáló vagy a ambari-ügynök szolgáltatásait, hacsak nem próbálja újraindítani a szolgáltatást a probléma megkerülése érdekében. A feladatátvétel kényszerítéséhez újra lehet indítani az aktív átjárócsomóponthoz.

Soha ne módosítsa manuálisan a konfigurációs fájlokat bármelyik fürtcsomóponton, hogy a Ambari felhasználói felülete elvégezze a feladatot.

## <a name="property-values-in-esp-clusters"></a>Tulajdonságértékek az ESP-fürtökben

A HDInsight 4,0 Enterprise Security Package-fürtökben nem vesszők, hanem csöveket használnak a `|` változó határolók helyett. Erre alább láthat egy példát:

```
Property Key: hive.security.authorization.sqlstd.confwhitelist.append
Property Value: environment|env|dl_data_dt
```

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
