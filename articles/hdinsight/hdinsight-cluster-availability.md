---
title: A fürt rendelkezésre állásának figyelése az Apache Ambari az Azure HDInsight
description: Ismerje meg, hogyan használható az Apache Ambari a fürt állapotának és rendelkezésre állásának figyelésére.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863530"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>A fürt rendelkezésre állásának figyelése az Apache Ambari az Azure HDInsight

A HDInsight-fürtök közé tartozik az Apache Ambari, amely egy pillantással és előre meghatározott riasztásokkal biztosítja az állapottal kapcsolatos információkat.

Ez a cikk azt mutatja be, hogyan használható a Ambari a fürt figyelésére, valamint néhány példa arra, hogyan konfigurálható a Ambari-riasztások, a csomópontok figyelési sebessége, valamint egy Azure Monitor riasztás létrehozása, amely akkor következik be, amikor egy szívverés nem érkezett le egy vagy több csomópontról öt órán belül.

## <a name="dashboard"></a>Irányítópult

A Ambari irányítópultja a következő Azure Portal a HDInsight áttekintésének **fürt irányítópultok** szakaszában a **Ambari Kezdőlap** hivatkozásra kattintva érhető el. Azt is megteheti, hogy egy böngészőben navigál, `https://CLUSTERNAME.azurehdinsight.net` ahol a CLUSTERNAME a fürt neve.

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="HDInsight erőforrás-portál nézet":::

Ezután meg kell adnia egy fürt bejelentkezési felhasználónevét és jelszavát. Adja meg a fürt létrehozásakor kiválasztott hitelesítő adatokat.

Ezután a Ambari-irányítópultra kerül, amely a HDInsight-fürt állapotának gyors áttekintését bemutató widgeteket tartalmaz. Ezek a minialkalmazások olyan metrikákat mutatnak, mint például az élő Adatcsomópontok (feldolgozó csomópontok) és a Naplócsomópontok (Zookeeper csomópont), a NameNodes (csomópontok), valamint bizonyos típusú fürtökhöz tartozó mérőszámok, például a Spark-és a erőforráskezelő-fürtök esetében a fonal Hadoop.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Az Apache Ambari irányítópult-megjelenítést használhat":::

## <a name="hosts--view-individual-node-status"></a>Gazdagépek – az egyes csomópontok állapotának megtekintése

Az egyes csomópontokra vonatkozó állapotinformációkat is megtekintheti. A **gazdagépek** lapon megtekintheti a fürt összes csomópontjának listáját, és megtekintheti az egyes csomópontok alapszintű információit. Az egyes csomópontok neve mezőtől balra található zöld pipa jelzi, hogy az összes összetevő fel van-e állítva a csomóponton. Ha egy összetevő le van kapcsolva egy csomóponton, a zöld pipa helyett piros riasztási háromszög jelenik meg.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="HDInsight Apache Ambari-gazdagépek nézet":::

Ezután kiválaszthatja a csomópont **nevét** az adott csomópont részletesebb gazdagép-metrikáinak megtekintéséhez. Ez a nézet az egyes összetevők állapotát és rendelkezésre állását jeleníti meg.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="Az Apache Ambari egyetlen csomópontos nézetet üzemeltet":::

## <a name="ambari-alerts"></a>Ambari-riasztások

A Ambari számos konfigurálható riasztást is kínál, amelyek bizonyos események bejelentését teszik lehetővé. A riasztások aktiválásakor a rendszer a Ambari bal felső sarkában jeleníti meg a riasztások számát tartalmazó piros jelvényt. A jelvény kiválasztásával megjeleníti az aktuális riasztások listáját.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Apache Ambari – aktuális riasztások száma":::

A riasztási definíciók és állapotuk megjelenítéséhez válassza a **riasztások** fület az alább látható módon.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Ambari-riasztások definíciói nézete":::

A Ambari számos előre meghatározott riasztást kínál a rendelkezésre állással kapcsolatban, beleértve a következőket:

| Riasztás neve                        | Leírás   |
|---|---|
| DataNode állapotának összegzése           | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha nem kifogástalan állapotú Adatcsomópontok van|
| NameNode magas rendelkezésre állási állapota | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha az aktív NameNode vagy a készenléti NameNode nem fut.|
| Rendelkezésre álló Naplócsomópontok százalékos aránya    | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Naplócsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a JournalNode folyamat-ellenőrzésének eredményeit. |
| Rendelkezésre álló Adatcsomópontok százalékos aránya       | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Adatcsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a DataNode folyamat-ellenőrzésének eredményeit.|


A riasztások részleteinek megtekintéséhez vagy a feltételek módosításához válassza ki a riasztás **nevét** . A **DataNode állapotának összegzése** példaként. Megtekintheti a riasztás leírását, valamint a "figyelmeztetés" vagy "kritikus" riasztást kiváltó konkrét feltételeket, valamint a feltételek ellenőrzési intervallumát is. A konfiguráció szerkesztéséhez kattintson a konfigurációs mező jobb felső sarkában található **Szerkesztés** gombra.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Apache Ambari-riasztás konfigurálása":::

Itt szerkesztheti a leírást, és – ami még fontosabb – a figyelmeztetési vagy kritikus riasztások ellenőrzési időközét és küszöbértékeit.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Ambari-riasztási konfigurációk szerkesztési nézete":::

Ebben a példában két nem kifogástalan állapotú Adatcsomópontok aktiválhat kritikus riasztást, és 1 nem kifogástalan állapotú DataNode csak figyelmeztetést indíthat. A Szerkesztés befejezése után válassza a **Mentés** lehetőséget.

## <a name="email-notifications"></a>E-mail-értesítések

A Ambari-riasztásokhoz e-mail-értesítéseket is konfigurálhat. Ehhez a **riasztások** lapon kattintson a bal felső sarokban található **műveletek** gombra, majd az **értesítések kezelése** lehetőségre.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Ambari – értesítések kezelése művelet":::

Ekkor megnyílik a riasztási értesítések kezelésére szolgáló párbeszédpanel. Kattintson a **+** párbeszédpanel alján található elemre, és töltse ki a kötelező mezőket az e-mail-kiszolgáló Ambari biztosításához, amelyről e-maileket szeretne küldeni.

> [!TIP]
> A Ambari e-mail-értesítéseinek beállítása jó módszer lehet a riasztások egy helyen való fogadására sok HDInsight-fürt kezelésekor.

## <a name="next-steps"></a>Következő lépések

- [Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsight-ben](./hdinsight-business-continuity.md)
- [Fürt rendelkezésre állása – Azure Monitor-naplók](./cluster-availability-monitor-logs.md)
- [Azure Monitor naplók használata](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Az Apache Ambari e-mail-értesítései](apache-ambari-email.md)
