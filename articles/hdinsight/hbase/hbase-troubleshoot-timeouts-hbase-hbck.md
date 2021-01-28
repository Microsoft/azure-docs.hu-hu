---
title: A hbase hbck parancs időtúllépése az Azure HDInsightban
description: Időtúllépési hiba az "hbase hbck" paranccsal a régió hozzárendeléseinek kijavításakor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: acf4d1237841f8c20c014598e00a5e8961e2a012
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936694"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Forgatókönyv: időtúllépés a "hbase hbck" paranccsal az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Időtúllépéseket észlel a `hbase hbck` régió-hozzárendelések kijavítására szolgáló paranccsal.

## <a name="cause"></a>Ok

Ha a parancs használatakor időtúllépési problémák merülhetnek fel, lehetséges `hbck` , hogy több régió "átmenetes" állapotban van hosszú ideig. Ezeket a régiókat kapcsolat nélküli üzemmódban láthatja a HBase Master felhasználói felületen. Mivel nagy számú régiót próbálnak áttérni, HBase Master lehet, hogy időtúllépés történt, és nem lehet ismét online állapotba hozni ezeket a régiókat.

## <a name="resolution"></a>Feloldás

1. Jelentkezzen be a HDInsight HBase-fürtbe SSH használatával.

1. Futtassa a `hbase zkcli` parancsot Apache ZooKeeper rendszerhéjhoz való kapcsolódáshoz.

1. Futtatás `rmr /hbase/regions-in-transition` vagy `rmr /hbase-unsecure/regions-in-transition` parancs.

1. Kilépés a `hbase zkcli` rendszerhéjból `exit` parancs használatával.

1. Az Apache Ambari felhasználói felületén indítsa újra az aktív HBase Master szolgáltatást.

1. Futtassa a következő parancsot: `hbase hbck -fixAssignments`.

1. Figyelje meg, hogy a "régió az átmenetben" HBase Master felhasználói felülete nem ragadja meg a régiót.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.