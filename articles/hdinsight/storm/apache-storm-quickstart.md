---
title: 'Rövid útmutató: Apache Storm topológia létrehozása/kezelése – Azure HDInsight'
description: A rövid útmutatóból megtudhatja, hogyan hozhat létre és figyelheti az Apache Storm topológiát az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.custom: mvc
ms.openlocfilehash: fe7b1cdc96ae95ac361183342ea491432c3ab4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928857"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Rövid útmutató: Apache Storm topológia létrehozása és figyelése az Azure HDInsight

Az Apache Storm egy skálázható, hibatűrő, elosztott, valós idejű számítási rendszer az adatstreamek feldolgozására. A Storm on Azure HDInsight segítségével olyan felhőalapú Storm-fürtöket hozhat létre, amelyek valós időben végeznek big data elemzést.

Ebben a rövid útmutatóban egy példát használ az Apache [Storm-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projektből egy Apache Storm topológia létrehozására és figyelésére egy meglévő Apache Storm-fürthöz.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Storm-fürt a HDInsight-on. Lásd: [hozzon létre Apache Hadoop fürtöket a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza a **Storm** a **fürt típusa** lehetőséget.

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>A topológia létrehozása

1. Kapcsolódjon a Storm-fürthöz. Szerkessze az alábbi parancsot úgy, hogy lecseréli a `CLUSTERNAME` Storm-fürt nevét, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A **WordCount** példa a HDInsight-fürtön található `/usr/hdp/current/storm-client/contrib/storm-starter/` . A topológia véletlenszerű mondatokat generál, és megszámolja, hogy hányszor fordulnak elő szavak. A következő parancs használatával indítsa el a fürtön a **WordCount** topológiát:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>A topológia figyelése

A Storm egy webes felületet biztosít a futó topológiák használatához, és a HDInsight-fürt részét képezi.

Kövesse az alábbi lépéseket a topológia a Storm felhasználói felületével történő figyeléséhez:

1. A Storm felhasználói felületének megjelenítéséhez egy webböngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net/stormui` oldalt. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

2. A **topológia összegzése** területen válassza ki a **WordCount** bejegyzést a **Name (név** ) oszlopban. Megjelennek a topológiával kapcsolatos információk.

    ![A Storm irányítópultja a Storm Starter WordCount-topológiára vonatkozó információkkal.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Az új oldal a következő információkat tartalmazza:

    |Tulajdonság | Leírás |
    |---|---|
    |Topológia statisztikái|Alapvető információk a topológia teljesítményéről, időablakok szerint rendezve. Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.|
    |Spoutok|Alapszintű információk a kiöntő szolgáltatásokról, beleértve az egyes kiöntő által visszaadott utolsó hibát is.|
    |Csavarok|A boltokkal kapcsolatos alapvető információk.|
    |Topológia konfigurációja|Részletes információk a topológia konfigurációjával kapcsolatban.|
    |Aktiválás|Folytatja a deaktivált topológia feldolgozását.|
    |Inaktiválás|Szünetelteti a futó topológiát.|
    |Újra egyensúlyba hozni|A topológia párhuzamosságának módosítása. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Az újraegyensúlyozás beállítja a párhuzamosságot a fürtben található csomópontok számának növekedése/csökkenése kiegyensúlyozása érdekében. További információ: [Apache Storm topológia párhuzamosságának megismerése](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Kill|A megadott időkorlát után leállítja a Storm-topológiát.|

3. Válassza ki a jelen lapon található **Spoutok** vagy **Boltok** szakaszok bejegyzéseinek egyikét. Ekkor információk jelennek meg a kiválasztott összetevővel kapcsolatban.

    ![A Storm irányítópultja a kiválasztott összetevőkkel kapcsolatos információkkal.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Az új oldalon az alábbi információk jelennek meg:

    |Tulajdonság | Leírás |
    |---|---|
    |Kiöntő/bolt statisztikái|Alapszintű információk a komponens teljesítményéről, időablakok szerint rendezve. Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.|
    |Bemeneti statisztika (csak bolt)|Információk a bolt által felhasznált adatokat előállító összetevőkről.|
    |Kimeneti statisztika|Információk a bolt által kibocsátott adatokról.|
    |Végrehajtók|Az összetevő példányaival kapcsolatos információk.|
    |Hibák|Az összetevő által létrehozott hibák.|

4. Egy adott spout vagy bolt részletes adatainak megtekintésekor az összetevők adott példánya részletes adatainak megtekintéséhez jelöljön ki egy bejegyzést a **Port** oszlopból az **Executors** (Végrehajtók) szakaszban.

```output
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
```

Ebben a példában a **seven** szó 1 493 957 alkalommal fordul elő. A szó előfordulásainak számlálása a topológia indításától kezdve történik.

## <a name="stop-the-topology"></a>A topológia leállítása

Lépjen vissza a **Topology summary** (Topológia összegzése) lapra a word-count topológiához, majd válassza a **Kill** (Törlés) gombot a **Topology actions** (Topológiaműveletek) szakaszban. Amikor a rendszer kéri, adjon meg 10 másodperces értéket a topológia leállítása előtti várakozási időként. Az időtúllépés lejáratát követően az adott topológia már nem jelenik meg az irányítópult **Storm felhasználói felülete** szakaszában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Apache [Storm-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projekt egy példáját használta egy Apache Storm topológia létrehozásához és figyeléséhez egy meglévő Apache Storm-fürthöz. Folytassa a következő cikkel, amely a Apache Storm topológiák kezelésével és figyelésével kapcsolatos alapvető tudnivalókat ismerteti.

> [!div class="nextstepaction"]
>[Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)