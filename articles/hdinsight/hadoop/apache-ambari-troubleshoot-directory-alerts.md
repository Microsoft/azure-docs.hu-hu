---
title: Apache Ambari Directory-riasztások az Azure HDInsight
description: Az Apache Ambari Directory-riasztások lehetséges okainak és megoldásainak megvitatása és elemzése a HDInsight-ben.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943277"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Forgatókönyv: Apache Ambari Directory-riasztások az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari az alábbihoz hasonló hibákat kap:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Ok

Az érintett munkavégző csomópont (ok) ból a Ambari-riasztásból említett könyvtárak hiányoznak.

## <a name="resolution"></a>Feloldás

Manuálisan hozza létre a hiányzó könyvtárakat az érintett munkavégző csomópont (ok) hoz.

1. SSH-t a megfelelő munkavégző csomóponthoz.

1. Root felhasználó beolvasása: `sudo su` .

1. A szükséges könyvtárak rekurzív létrehozása.

1. A könyvtárak tulajdonosának és csoportjának módosítása.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Az Apache Ambari felhasználói felületén tiltsa le, majd engedélyezze a riasztást.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]