---
title: Rögzített CPU az Apache HBase-fürtben – Azure HDInsight
description: Az Azure HDInsight-ben elérhető Apache HBase-fürtön található, a tartományban található, rögzített CPU-kiszolgáló hibáinak megoldása
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: ab9aa6d7be83faa0c26951b2c45092bbcb063d79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540210"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Forgatókönyv: rögzített CPU a régió-kiszolgálón az Apache HBase-fürtben az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase-régió kiszolgálói folyamata a 200%-os CPU-hoz közeledik, ami miatt a riasztások HBase Master folyamaton és fürtön nem működnek teljes kapacitással.

## <a name="cause"></a>Ok

Ha a HBase-fürt v 3.4-es verzióját futtatja, lehetséges, hogy a JDK-t a 1.7.0 _151 verziójára való frissítés okozta. A megjelenő tünet a régió kiszolgálója, amely a következőhöz közeledik: 200% CPU (annak ellenőrzéséhez, hogy ez a parancs fut-e `top` . ha van olyan folyamat, amely az 200%-os CPU-hoz csatlakozik, kérje le a PID-t, és ellenőrizze, hogy fut-e a régió kiszolgáló `ps -aux | grep`

## <a name="resolution"></a>Feloldás

1. Telepítse a jdk 1,8-et a fürt összes csomópontján az alábbiak szerint:

    * Futtassa a parancsfájl-műveletet `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Ügyeljen arra, hogy az összes csomóponton a Futtatás lehetőséget válassza.

    * Másik lehetőségként bejelentkezhet minden egyes csomópontba, és futtathatja a parancsot `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Ugrás a Ambari felhasználói felületére – `https://<clusterdnsname>.azurehdinsight.net` .

1. Navigáljon a **HBase->konfigurációk->Advanced – >Advanced** (speciális) elemre `hbase-env configs` , és módosítsa a változót a következőre: `JAVA_HOME` `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Mentse a konfiguráció módosítását.

1. [Nem kötelező, de ajánlott] [Az összes tábla kiürítése a fürtön](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. A Ambari felhasználói felületéről indítsa újra az összes újraindítást igénylő HBase-szolgáltatást.

1. A fürtön lévő adattól függően előfordulhat, hogy akár egy óráig is eltarthat, amíg a fürt stabil állapotba nem ér. A fürt megerősítésének módja a HMaster felhasználói felületének ellenőrzése (az összes régió-kiszolgáló aktívnak kell lennie) a Ambari (frissítés) vagy a átjárócsomóponthoz Run HBase shell, majd az status parancs futtatásával.

Annak ellenőrzéséhez, hogy a frissítés sikeres volt-e, ellenőrizze, hogy az adott HBase-folyamatok a megfelelő Java-verzióval kezdődnek-e, például a régió-kiszolgáló ellenőrzéséhez a következő módon:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.