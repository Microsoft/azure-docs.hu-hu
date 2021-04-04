---
title: Az Apache Ambari TEZ View lassan töltődik be az Azure HDInsight
description: Előfordulhat, hogy az Apache Ambari TEZ-nézete lassan vagy egyáltalán nem töltődik be az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930770"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Forgatókönyv: az Apache Ambari TEZ-nézete lassan töltődik be az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Előfordulhat, hogy az Apache Ambari TEZ-nézete lassan vagy egyáltalán nem töltődik be. Ambari TEZ-nézet betöltésekor előfordulhat, hogy a Átjárócsomópontokkal folyamatai nem válaszolnak.

## <a name="cause"></a>Ok

A fonál ATS API-k elérése esetenként gyenge teljesítményt eredményezhet a 2017 TOT előtt létrehozott fürtökön, amikor a fürt nagy számú kaptár-feladatot futtat rajta.

## <a name="resolution"></a>Feloldás

Ez egy olyan probléma, amely a 2017-es TOT-ban lett kijavítva. Ha újra létrehozza a fürtöt, azzal nem fogja újból futtatni a problémát.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]