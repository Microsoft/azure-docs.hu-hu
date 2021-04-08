---
title: Apache TEZ-alkalmazás lefagy az Azure HDInsight
description: Apache TEZ-alkalmazás lefagy az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935941"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Forgatókönyv: az Apache TEZ-alkalmazás lefagy az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache Hive-feladatoknak a TEZ való elküldése után a feladatok állapota "fut", de úgy tűnik, hogy nem végez előrehaladást

## <a name="cause"></a>Ok

Túl sok feladat van elküldve; hosszú fonalas üzenetsor.

## <a name="resolution"></a>Feloldás

Bővítse a fürtöt, vagy csak várjon, amíg a fonal-várólista el nem kerül.

Alapértelmezés szerint `yarn.scheduler.capacity.maximum-applications` a vagy függőben lévő alkalmazások maximális számát szabályozza, és alapértelmezés szerint a () értéket használja `10000` .

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.