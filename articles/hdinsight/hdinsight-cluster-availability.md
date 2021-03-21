---
title: A fürt rendelkezésre állásának figyelése az Apache Ambari az Azure HDInsight
description: Ismerje meg, hogyan használható az Apache Ambari a fürt állapotának és rendelkezésre állásának figyelésére.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 34f752d4c3d50d5f680e317b2724b1e631537f32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933189"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>A fürt rendelkezésre állásának figyelése az Apache Ambari az Azure HDInsight

A HDInsight-fürtök közé tartozik az Apache Ambari, amely egy pillantással és előre meghatározott riasztásokkal biztosítja az állapottal kapcsolatos információkat.

Ez a cikk azt mutatja be, hogyan használható a Ambari a fürt figyelésére, valamint néhány példa arra, hogyan konfigurálható a Ambari-riasztások, a csomópontok figyelési sebessége, valamint egy Azure Monitor riasztás létrehozása, amely akkor következik be, amikor egy szívverés nem érkezett le egy vagy több csomópontról öt órán belül.

## <a name="dashboard"></a>Irányítópult

A Ambari irányítópultja a következő Azure Portal a HDInsight áttekintésének **fürt irányítópultok** szakaszában a **Ambari Kezdőlap** hivatkozásra kattintva érhető el. Azt is megteheti, hogy egy böngészőben navigál, `https://CLUSTERNAME.azurehdinsight.net` ahol a CLUSTERNAME a fürt neve.

![HDInsight erőforrás-portál nézet](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Ezután meg kell adnia egy fürt bejelentkezési felhasználónevét és jelszavát. Adja meg a fürt létrehozásakor kiválasztott hitelesítő adatokat.

Ezután a Ambari-irányítópultra kerül, amely a HDInsight-fürt állapotának gyors áttekintését bemutató widgeteket tartalmaz. Ezek a minialkalmazások olyan metrikákat mutatnak, mint például az élő Adatcsomópontok (feldolgozó csomópontok) és a Naplócsomópontok (Zookeeper csomópont), a NameNodes (csomópontok), valamint bizonyos típusú fürtökhöz tartozó mérőszámok, például a Spark-és a erőforráskezelő-fürtök esetében a fonal Hadoop.

![Az Apache Ambari irányítópult-megjelenítést használhat](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>Gazdagépek – az egyes csomópontok állapotának megtekintése

Az egyes csomópontokra vonatkozó állapotinformációkat is megtekintheti. A **gazdagépek** lapon megtekintheti a fürt összes csomópontjának listáját, és megtekintheti az egyes csomópontok alapszintű információit. Az egyes csomópontok neve mezőtől balra található zöld pipa jelzi, hogy az összes összetevő fel van-e állítva a csomóponton. Ha egy összetevő le van kapcsolva egy csomóponton, a zöld pipa helyett piros riasztási háromszög jelenik meg.

![HDInsight Apache Ambari-gazdagépek nézet](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Ezután kiválaszthatja a csomópont **nevét** az adott csomópont részletesebb gazdagép-metrikáinak megtekintéséhez. Ez a nézet az egyes összetevők állapotát és rendelkezésre állását jeleníti meg.

![Az Apache Ambari egyetlen csomópontos nézetet üzemeltet](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Ambari-riasztások

A Ambari számos konfigurálható riasztást is kínál, amelyek bizonyos események bejelentését teszik lehetővé. A riasztások aktiválásakor a rendszer a Ambari bal felső sarkában jeleníti meg a riasztások számát tartalmazó piros jelvényt. A jelvény kiválasztásával megjeleníti az aktuális riasztások listáját.

![Apache Ambari – aktuális riasztások száma](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

A riasztási definíciók és állapotuk megjelenítéséhez válassza a **riasztások** fület az alább látható módon.

![Ambari-riasztások definíciói nézete](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

A Ambari számos előre meghatározott riasztást kínál a rendelkezésre állással kapcsolatban, beleértve a következőket:

| Riasztás neve                        | Description   |
|---|---|
| DataNode állapotának összegzése           | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha nem kifogástalan állapotú Adatcsomópontok van|
| NameNode magas rendelkezésre állási állapota | Ez a szolgáltatási szintű riasztás akkor aktiválódik, ha az aktív NameNode vagy a készenléti NameNode nem fut.|
| Rendelkezésre álló Naplócsomópontok százalékos aránya    | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Naplócsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a JournalNode folyamat-ellenőrzésének eredményeit. |
| Rendelkezésre álló Adatcsomópontok százalékos aránya       | Ez a riasztás akkor aktiválódik, ha a fürtben lévő lefelé Adatcsomópontok száma nagyobb, mint a beállított kritikus küszöbérték. Összesíti a DataNode folyamat-ellenőrzésének eredményeit.|


A riasztások részleteinek megtekintéséhez vagy a feltételek módosításához válassza ki a riasztás **nevét** . A **DataNode állapotának összegzése** példaként. Megtekintheti a riasztás leírását, valamint a "figyelmeztetés" vagy "kritikus" riasztást kiváltó konkrét feltételeket, valamint a feltételek ellenőrzési intervallumát is. A konfiguráció szerkesztéséhez kattintson a konfigurációs mező jobb felső sarkában található **Szerkesztés** gombra.

![Apache Ambari-riasztás konfigurálása](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Itt szerkesztheti a leírást, és – ami még fontosabb – a figyelmeztetési vagy kritikus riasztások ellenőrzési időközét és küszöbértékeit.

![Ambari-riasztási konfigurációk szerkesztési nézete](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Ebben a példában két nem kifogástalan állapotú Adatcsomópontok aktiválhat kritikus riasztást, és 1 nem kifogástalan állapotú DataNode csak figyelmeztetést indíthat. A Szerkesztés befejezése után válassza a **Mentés** lehetőséget.

## <a name="email-notifications"></a>E-mail-értesítések

A Ambari-riasztásokhoz e-mail-értesítéseket is konfigurálhat. Ehhez a **riasztások** lapon kattintson a bal felső sarokban található **műveletek** gombra, majd az **értesítések kezelése** lehetőségre.

![Ambari – értesítések kezelése művelet](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Ekkor megnyílik a riasztási értesítések kezelésére szolgáló párbeszédpanel. Kattintson a **+** párbeszédpanel alján található elemre, és töltse ki a kötelező mezőket az e-mail-kiszolgáló Ambari biztosításához, amelyről e-maileket szeretne küldeni.

> [!TIP]
> A Ambari e-mail-értesítéseinek beállítása jó módszer lehet a riasztások egy helyen való fogadására sok HDInsight-fürt kezelésekor.

## <a name="next-steps"></a>Következő lépések

- [Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsight-ben](./hdinsight-business-continuity.md)
- [Fürt rendelkezésre állása – Azure Monitor-naplók](./cluster-availability-monitor-logs.md)
- [Azure Monitor naplók használata](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Az Apache Ambari e-mail-értesítései](apache-ambari-email.md)
