---
title: Hiba az Azure HDInsight-fürtön
description: Az Azure HDInsight-fürtben található kernel syslogs szolgáltatásban megjelenik a watchdog BUG Soft kizárható PROCESSZORa
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944335"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Forgatókönyv: "watchdog: BUG: Soft lefagyás-CPU" hiba egy Azure HDInsight-fürtből

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A kernel syslogs a következő hibaüzenetet tartalmazza: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Ok

A Linux kernel [hibája](https://bugzilla.kernel.org/show_bug.cgi?id=199437) okozza a processzorok lágy lefagyását.

## <a name="resolution"></a>Feloldás

Kernel-javítás alkalmazása. Az alábbi szkript frissíti a Linux-kernelt, és 24 órán belül különböző időpontokban újraindítja a gépeket. Hajtsa végre a parancsfájl-műveletet két kötegben. Az első köteg az összes csomóponton van, kivéve a fő csomópontot. A második köteg a fő csomóponton található. Ne futtassa a főcsomópontot és más csomópontokat egyszerre.

1. Navigáljon a HDInsight-fürt Azure Portal.

1. Ugrás a parancsfájlok műveleteire.

1. Válassza az **új küldése** lehetőséget, és adja meg a bemenetet a következőképpen

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl típusa | – Egyéni |
    | Name |A kernel Soft Lock hibájának javítása |
    | Bash-parancsfájl URI-ja |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Csomópont típusa (i) |Worker, Zookeeper |
    | Paraméterek |N/A |

    Válassza a parancsfájl megtartása **művelet...** lehetőséget, ha azt szeretné, hogy az új csomópontok hozzáadásakor végrehajtsa a parancsfájlt.

1. Válassza a **Létrehozás** lehetőséget.

1. Várjon, amíg a végrehajtás sikeres lesz.

1. Hajtsa végre a parancsfájl műveletét a fej csomóponton a 3. lépéssel megegyező lépéseket követve, de ezúttal a következő csomópont-típusokkal: Head.

1. Várjon, amíg a végrehajtás sikeres lesz.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]