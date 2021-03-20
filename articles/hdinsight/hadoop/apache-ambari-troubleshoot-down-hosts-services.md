---
title: Az Apache Ambari felhasználói felülete a gazdagépeket és szolgáltatásokat mutatja be az Azure HDInsight
description: Apache Ambari felhasználói felülettel kapcsolatos probléma elhárítása, amikor az Azure HDInsight gazdagépeit és szolgáltatásait mutatja be
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943263"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Forgatókönyv: az Apache Ambari felhasználói felülete a gazdagépeket és szolgáltatásokat mutatja be az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari felhasználói felülete elérhető, de a felhasználói felület szinte minden szolgáltatás leállt, a szívverést jelző összes gazdagép elveszett.

## <a name="cause"></a>Ok

A legtöbb esetben ez az a probléma, hogy a Ambari-kiszolgáló nem fut az aktív átjárócsomóponthoz. Ellenőrizze, hogy melyik átjárócsomóponthoz az aktív átjárócsomóponthoz, és győződjön meg arról, hogy a ambari-kiszolgáló fut a jobb oldalon. Ne indítsa el kézzel a ambari, hagyja, hogy a feladatátvételi vezérlő szolgáltatás felelős a ambari a megfelelő átjárócsomóponthoz való indításához. Indítsa újra az aktív átjárócsomóponthoz a feladatátvétel kényszerítéséhez.

A hálózati problémák is okozhatják ezt a problémát. A fürt minden csomópontján ellenőrizze, hogy tud-e pingelni `headnodehost` . Ritka helyzet van, ha egy fürtcsomópont nem tud csatlakozni a következőhöz `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Feloldás

Általában az aktív átjárócsomóponthoz újraindítása csökkenti a problémát. Ha nem forduljon a HDInsight ügyfélszolgálatához.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]