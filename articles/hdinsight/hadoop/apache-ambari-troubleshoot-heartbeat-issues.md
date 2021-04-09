---
title: Az Apache Ambari szívveréssel kapcsolatos problémái az Azure HDInsightban
description: Az Apache Ambari szívverésével kapcsolatos problémák különböző okainak áttekintése az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946740"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Az Apache Ambari szívveréssel kapcsolatos problémái az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-high-cpu-utilization"></a>Forgatókönyv: magas CPU-kihasználtság

### <a name="issue"></a>Probléma

A Ambari-ügynök magas CPU-kihasználtsággal rendelkezik, ami a Ambari felhasználói felületének riasztásait eredményezi, hogy egyes csomópontok esetén a Ambari-ügynök szívverése megszakad. A szívverés elveszett riasztása általában átmeneti.

### <a name="cause"></a>Ok

A különböző ambari hibák miatt előfordulhat, hogy a ambari-ügynöknek magas (100) százalékos CPU-kihasználtsága lehet.

### <a name="resolution"></a>Feloldás

1. Azonosítsa a ambari-ügynök folyamat-AZONOSÍTÓját (PID):

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Ezután futtassa a következő parancsot a processzorhasználat megjelenítéséhez:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. A probléma megoldásához indítsa újra a ambari-Agent ügynököt:

    ```bash
    service ambari-agent restart
    ```

1. Ha az újraindítás nem működik, öld meg a ambari folyamatot, majd indítsa el:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Forgatókönyv: a Ambari-ügynök nem indult el

### <a name="issue"></a>Probléma

A Ambari-ügynök nem indult el, amely a Ambari felhasználói felületén riasztásokat eredményez, hogy egyes csomópontok esetében a Ambari-ügynök szívverése megszakad.

### <a name="cause"></a>Ok

A riasztások oka a Ambari-ügynök nem fut.

### <a name="resolution"></a>Feloldás

1. Ambari-ügynök állapotának megerősítése:

    ```bash
    service ambari-agent status
    ```

1. Ellenőrizze, hogy futnak-e a feladatátvételi vezérlő szolgáltatásai:

    ```bash
    ps -ef | grep failover
    ```

    Ha a feladatátvételi vezérlő szolgáltatásai nem futnak, valószínűleg egy probléma okozza, hogy a hdinsight-ügynök nem indítja el a feladatátvételi vezérlőt. A fájlból olvassa be a hdinsight-ügynök naplóját `/var/log/hdinsight-agent/hdinsight-agent.out` .

## <a name="scenario-heartbeat-lost-for-ambari"></a>Forgatókönyv: elveszett szívverés a Ambari esetében

### <a name="issue"></a>Probléma

A Ambari szívverés ügynöke megszakadt.

### <a name="cause"></a>Ok

A OMS-naplók nagy CPU-kihasználtságot okoznak.

### <a name="resolution"></a>Feloldás

* Tiltsa le Azure Monitor naplózást a [disable-AzHDInsightMonitoring PowerShell-](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) parancsmag használatával.
* A `mdsd.warn` naplófájl törlése

---

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]