---
title: Nem lehet bejelentkezni az Azure HDInsight-fürtbe
description: A Apache Hadoop-fürt Azure HDInsight való bejelentkezésének hibáinak megoldása
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944309"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]