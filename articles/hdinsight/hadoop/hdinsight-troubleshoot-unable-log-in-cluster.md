---
title: Nem lehet bejelentkezni az Azure HDInsight-fürtbe
description: A Apache Hadoop-fürt Azure HDInsight való bejelentkezésének hibáinak megoldása
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 059fee89fdb7f0bbc046c98e36c6386d06653fff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540414"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Forgatókönyv: nem lehet bejelentkezni az Azure HDInsight-fürtbe

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet bejelentkezni az Azure HDInsight-fürtbe.

## <a name="cause"></a>Ok

Az okok eltérőek lehetnek. Ne feledje, hogy amikor bejelentkezik a fürtbe vagy az alkalmazás irányítópultokra, használja a "fürt bejelentkezési adatait" vagy a HTTP-hitelesítő adatokat. Távoli kapcsolódás esetén az SSH vagy a Távoli asztal hitelesítő adatait használja.

## <a name="resolution"></a>Feloldás

A leggyakoribb hibákat az alábbi lépéseket követve háríthatja el.

* Nyissa meg a Fürt irányítópultját bizalmassági módban egy új böngészőlapon.

* Ha nem tudja felidézni az SSH hitelesítő adatait, [alaphelyzetbe állíthatja a hitelesítő adatokat a Ambari felhasználói felületén](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.