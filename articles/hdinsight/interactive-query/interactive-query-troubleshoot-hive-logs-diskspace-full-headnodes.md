---
title: 'Hibakeresés: Apache Hive naplófájlok kitöltése lemezterület – Azure HDInsight'
description: Ez a cikk azokat a hibaelhárítási lépéseket ismerteti, amikor Apache Hive naplók kitöltik a lemezterületet az Azure HDInsight lévő fő csomópontokon.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930947"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Forgatókönyv: Apache Hive naplók kitöltik a lemezterületet az Azure HDInsight lévő fő csomópontokon

Ez a cikk az Azure HDInsight-fürtök főcsomópontjain a nem elegendő lemezterülettel kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy Apache Hive-vagy LLAP-fürtön a nem kívánt naplók a teljes lemezterületet veszik fel a fő csomópontokon. Ez az állapot a következő problémákhoz vezethet:

- Az SSH-hozzáférés meghiúsul, mert nincs szóköz a fő csomóponton.
- Ambari-dob *http-hiba: a 503 szolgáltatás nem érhető el*.
- A HiveServer2 Interactive nem tud újraindulni.

A `ambari-agent` naplók a következő bejegyzéseket fogják tartalmazni a probléma bekövetkezésekor:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Ok

A speciális kaptár log4j-konfigurációk esetében a jelenlegi alapértelmezett törlési ütemterv a 30 napnál régebbi fájlok törlése az utolsó módosítás dátuma alapján.

## <a name="resolution"></a>Feloldás

1. Nyissa meg a kaptár-összetevő összegzését a Ambari-portálon, és válassza a **konfigurációk** lapot.

2. Nyissa meg a `Advanced hive-log4j` **Speciális beállítások** szakaszát.

3. Állítsa a `appender.RFA.strategy.action.condition.age` paramétert egy tetszőleges korra. Ez a példa az életkort 14 napra állítja be: `appender.RFA.strategy.action.condition.age = 14D`

4. Ha nem látja a kapcsolódó beállításokat, fűzze hozzá ezeket a beállításokat:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. A értékre állítva `hive.root.logger` `INFO,RFA` az alábbi példában látható módon. Az alapértelmezett beállítás a `DEBUG` , amely a naplókat nagy méretűvé teszi.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Mentse a konfigurációkat, és indítsa újra a szükséges összetevőket.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
