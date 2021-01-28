---
title: Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsight
description: Megtudhatja, hogyan helyezheti üzembe, figyelheti és kezelheti Apache Storm topológiákat a Linux-alapú HDInsight található Storm irányítópult használatával. A Visual studióhoz készült Hadoop Tools használata.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 417819cad3bc2ee258381426dfcee7c800b69d42
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929169"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Apache Storm-topológiák üzembe helyezése és kezelése az Azure HDInsight

Ebből a dokumentumból megtudhatja, hogyan kezelheti és figyelheti [Apache Storm](https://storm.apache.org/) a Storm on HDInsight-fürtökön futó topológiákat.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Storm-fürt a HDInsight-on. Lásd: [hozzon létre Apache Hadoop fürtöket a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza a **Storm** a **fürt típusa** lehetőséget.

* Választható A Secure Shell (SSH) és a biztonságos másolás (SCP) ismerete. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Választható A Visual Studio, az Azure SDK 2.5.1-es vagy újabb verziója, valamint a Visual studióhoz készült Data Lake Tools. További információ: [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Topológia elküldése a Visual Studióval

A Visual studióhoz készült Data Lake Tools használatával C# vagy hibrid topológiát küldhet a Storm-fürtbe. A következő lépések egy minta alkalmazást használnak. További információ a topológia létrehozásáról a Data Lake eszközökkel: [Apache Storm-topológiák a Visual Studióval és a C#](apache-storm-develop-csharp-visual-studio-topology.md)használatával.

1. Ha még nem telepítette a Data Lake Tools for Visual Studio legújabb verzióját, tekintse meg a [Data Lake Tools for Visual Studio használatát](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)ismertető témakört.

    > [!NOTE]  
    > A Azure Data Lake és Stream Analytics eszközöket korábban a Visual studióhoz készült HDInsight-eszközök hívták.
    >
    > A Visual studióhoz készült Azure Data Lake és Stream Analytics eszközök a Visual Studio 2019 **Azure-fejlesztési** számítási feladatának részét képezik.

1. Indítsa el a Visual Studiót.

1. A **Start** ablakban válassza az **új projekt létrehozása** lehetőséget.

1. A **create a New Project (új projekt létrehozása** ) ablakban jelölje be a keresőmezőbe, majd írja be a kifejezést `Storm` . Ezután válassza a **Storm Sample** elemet az eredmények listájában, és kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** ablakban adja meg a **projekt nevét**, és lépjen a helyre, vagy hozzon létre egy **helyet** az új projekt mentéséhez a alkalmazásban. Ezután kattintson a **Létrehozás** elemre.

    ![Az új Project-ablak, a Visual Studio konfigurálása](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. A **Server Explorerben** kattintson a jobb gombbal az **Azure** elemre, és válassza a **Kapcsolódás Microsoft Azure előfizetéshez...** lehetőséget, és fejezze be a bejelentkezési folyamatot.

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **Küldés a Storm on HDInsight** lehetőséget.

    > [!NOTE]  
    > Ha a rendszer kéri, adja meg az Azure-előfizetéséhez tartozó bejelentkezési hitelesítő adatokat. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be a HDInsight-fürtön található Stormot tartalmazóba.

1. A **topológia beküldése** párbeszédpanel **Storm-fürt** legördülő listájában válassza ki a Storm on HDInsight-fürtöt, majd válassza a **Küldés** lehetőséget. A **kimenet** ablaktáblán megtekintheti, hogy a Küldés sikeres volt-e.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Topológia beküldése az SSH és a Storm parancs használatával

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetben használja a következő parancsot a **WordCount** példa topológiájának elindításához:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Ez a parancs elindítja a fürtön a WordCount példatopológiát. Ez a topológia véletlenszerűen generál mondatokat, majd megszámolja az egyes szavak előfordulását a mondatokban.

    > [!NOTE]  
    > Amikor topológiát küld a fürtnek, előbb át kell másolnia a fürtöt tartalmazó. jar-fájlt a parancs használata előtt `storm` . A fájlnak a fürtbe való másolásához használhatja az `scp` parancsot. Adja meg például a következőt: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > A *WordCount* példát és más Storm Starter-példákat már tartalmaz a fürtben `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Topológia beküldése programozott módon

A Nimbus szolgáltatás használatával programozott módon helyezhet üzembe egy topológiát. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) egy példás Java-alkalmazást biztosít, amely bemutatja, hogyan helyezhet üzembe és indíthat el topológiát a Nimbus szolgáltatáson keresztül.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Topológia figyelése és kezelése a Visual Studióban

Amikor a Visual Studióval küldi el a topológiát, megjelenik a **Storm-topológiák nézet** ablak. Válassza ki a topológiát a listából a futó topológiával kapcsolatos információk megtekintéséhez.

![Figyelő topológia, Storm-topológiák nézet ablak, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> A **Storm-topológiákat** a **Server Explorerben** is megtekintheti. Bontsa ki az **Azure**  >  **HDInsight** csomópontot, kattintson a jobb gombbal a HDInsight-fürtön található Storm elemre, majd válassza a **Storm-topológiák megtekintése** lehetőséget.

Válassza ki a kiöntő vagy a bolt alakzatát, és tekintse meg az ezen összetevőkre vonatkozó információkat. A kiválasztott elemhez tartozó elemleírás jelenik meg.

### <a name="deactivate-and-reactivate-a-topology"></a>Topológia inaktiválása és újraaktiválása

A topológia inaktiválása szünetelteti a topológiát, amíg a rendszer nem távolítja el vagy nem aktiválja újra. Ezeknek a műveleteknek a végrehajtásához a **Storm-topológiák nézet** ablak felső részén található **műveletek** területen a **deaktiválás** és **újraaktiválás** gombokat használhatja.

### <a name="rebalance-a-topology"></a>Topológia újraelosztása

A topológia újraelosztása lehetővé teszi a rendszer számára a topológia párhuzamosságának módosítását. Ha például átméretezi a fürtöt a további megjegyzések hozzáadásához, akkor az újraelosztás lehetővé teszi, hogy az új csomópontok lássák a topológiát.

A topológia újraelosztásához használja a **Storm-topológiák nézet** ablak **műveletek** területén található **újraelosztás** gombot.

> [!WARNING]  
> A topológia újraelosztása inaktiválja a topológiát, a feldolgozókat egyenletesen osztja szét a fürtön, majd visszaküldi a topológiát arra az állapotra, amely az újrakiegyensúlyozás előtt volt. Ha a topológia aktív volt, az aktívvá válik. Ha a topológia inaktiválása megszakadt, inaktiválva marad.

### <a name="kill-a-running-topology"></a>Futó topológia leölése

A Storm-topológiák addig futnak, amíg le nem állnak, vagy a fürtöt törölték. A topológia leállításához használja a **műveletek** területen található **kill** gombot.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Topológia figyelése és kezelése SSH-val és a Storm parancs használatával

A `storm` segédprogram lehetővé teszi, hogy a parancssorból futtassa a futó topológiákat. A `storm -h` parancsok teljes listáját használja.

### <a name="list-topologies"></a>Topológiák listázása

A következő parancs használatával listázhatja az összes futó topológiát:

```shell
storm list
```

Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Topológia inaktiválása és újraaktiválása

A topológia inaktiválása szünetelteti a topológiát, amíg a rendszer nem távolítja el vagy nem aktiválja újra. Az inaktiváláshoz vagy az újraaktiváláshoz használja az alábbi parancsokat:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Futó topológia leölése

Storm-topológiák – az indítás után folytassa a futtatást a Leállításig. A topológia leállításához használja a következő parancsot:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Topológia újraelosztása

A topológia újraelosztása lehetővé teszi a rendszer számára a topológia párhuzamosságának módosítását. Ha például átméretezi a fürtöt a további megjegyzések hozzáadásához, akkor az újraelosztás lehetővé teszi, hogy az új csomópontok lássák a topológiát.

> [!WARNING]  
> A topológia újraelosztása inaktiválja a topológiát, a feldolgozókat egyenletesen osztja szét a fürtön, majd visszaküldi a topológiát arra az állapotra, amely az újrakiegyensúlyozás előtt volt. Ha a topológia aktív volt, az aktívvá válik. Ha inaktiválva van, inaktiválva marad.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Topológia figyelése és kezelése a Storm felhasználói felületének használatával

A Storm felhasználói felülete egy webes felületet biztosít a futó topológiák használatához, és a HDInsight-fürtben is megtalálható. A Storm felhasználói felületének megtekintéséhez nyisson meg egy böngészőt `https://CLUSTERNAME.azurehdinsight.net/stormui` , ahol a *CLUSTERNAME* a fürt neve.

> [!NOTE]  
> Ha a rendszer a Felhasználónév és a jelszó megadását kéri, adja meg a fürt létrehozásakor használt Fürtfelügyelő-felhasználónevet és-jelszót.

### <a name="storm-ui-main-page"></a>Storm felhasználói felület főoldala

A Storm felhasználói felületének fő lapja a következő információkat tartalmazza:

| Section | Description |
| --- | --- |
| Fürt összegzése| Alapszintű információk a Storm-fürtről. |
| Nimbus összegzése | Az alapvető Nimbus-információk listája. |
| Topológia összegzése | A futó topológiák listája. Egy adott topológiával kapcsolatos további információk megtekintéséhez válassza ki a hivatkozást a Name ( **név** ) oszlopban. |
| Felügyelői összefoglalás | A Storm felettesének információi. Egy adott feletteshez társított munkavégző erőforrások megtekintéséhez válassza ki a hivatkozást a **gazdagép** vagy az **azonosító** oszlopban. |
| Nimbus-konfiguráció | Nimbus-konfiguráció a fürthöz. |

A Storm UI főoldala a következő weboldalhoz hasonlóan néz ki:

![Főoldal, Storm UI, Apache Storm topológiák, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topológia összegzése

A **topológia összegzése** szakasz egy hivatkozásának kiválasztásával az alábbi információk jelennek meg a topológiával kapcsolatban:

| Section | Description |
| --- | --- |
| Topológia összegzése | A topológiával kapcsolatos alapvető információk. |
| Topológiai műveletek| A topológiához elvégezhető felügyeleti műveletek. Az elérhető műveleteket a szakasz későbbi részében ismertetjük. |
| Topológia statisztikái | A topológiával kapcsolatos statisztikák. Az ebben a szakaszban szereplő bejegyzés időkeretének megadásához válassza ki a hivatkozást az **ablak** oszlopban. |
| Kiöntő *(időkeret)* | A topológia által használt kiöntők. Egy adott kiöntőről további információk megtekintéséhez válassza ki a hivatkozást az **azonosító** oszlopban. |
| Csavarok *(időkeret)* | A topológia által használt csavarok. Egy adott bolttal kapcsolatos további információk megtekintéséhez válassza ki a hivatkozást az **azonosító** oszlopban. |
| Munkavégző erőforrások | A munkavégző erőforrások listája. Egy adott feldolgozó erőforrással kapcsolatos további információk megtekintéséhez válassza ki a hivatkozást a **gazdagép** oszlopban. |
| Topológia vizualizációja | **Vizualizáció megjelenítése** gomb, amely megjeleníti a topológia vizualizációját. |
| Topológia konfigurációja | A kiválasztott topológia konfigurációja. |

A Storm-topológia összefoglalás lapja ehhez a weboldalhoz hasonlóan néz ki:

![Topológia összegzése lap, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

A **topológiai műveletek** szakaszban a következő gombokra kattintva végezheti el a műveletet:

| Gomb | Description |
| --- | --- |
| Aktiválás | Folytatja a deaktivált topológia feldolgozását. |
| Inaktiválás | Szünetelteti a futó topológiát. |
| Újra egyensúlyba hozni | A topológia párhuzamosságának módosítása. Ha módosította a fürtben lévő csomópontok számát, érdemes a futó topológiákat újra kiegyenlíteni. Ez a művelet lehetővé teszi, hogy a topológia a párhuzamosságot úgy állítsa be, hogy kompenzálja a fürtben lévő csomópontok további vagy kevesebb számát.<br/><br/>További információ: <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm topológia párhuzamosságának megismerése</a>.
| Kill | A megadott időkorlát után leállítja a Storm-topológiát. |
| Hibakeresés | Elindítja a futó topológia hibakeresési munkamenetét. |
| Hibakeresés leállítása | A futó topológia hibakeresési munkamenetének vége. |
| Napló szintjének módosítása | A hibakeresési napló szintjének módosítása. |

##### <a name="spout-and-bolt-summary"></a>Kiöntő és bolt összefoglalása

**A kiöntő vagy a** **boltokból** származó kiöntő kiválasztásakor az alábbi információk jelennek meg a kijelölt elemről:

| Section | Description |
| --- | --- |
| Összetevő összegzése | Alapszintű információk a kiöntő vagy a boltról. |
| Összetevő-műveletek | **Hibakeresés** és **Leállítás** gombokkal. |
| Kiöntő statisztika vagy bolt statisztikái | A kiöntő vagy a bolt statisztikája. Az ebben a szakaszban szereplő bejegyzés időkeretének megadásához válassza ki a hivatkozást az **ablak** oszlopban. |
| (Csak bolt)<br/>Bemeneti statisztika *(időkeret)* | A bolt által felhasznált bemeneti adatfolyamokra vonatkozó információk. |
| Kimeneti statisztika *(időkeret)* | Információk a kiöntő vagy a bolt által kibocsátott adatfolyamokról. |
| Profilkészítés és hibakeresés | Az ezen a lapon található összetevők profilkészítésének és hibakeresésének vezérlése. Megadhatja az **állapot/időkorlát (perc)** értéket, és kiválaszthatja a **JStack**, a **feldolgozót** és a **kupacot** is. |
| Végrehajtók *(időkeret)* | Információk a kiöntő vagy a bolt példányairól. Ha meg szeretné tekinteni az ehhez a példányhoz létrehozott diagnosztikai információk naplóját, válassza ki az adott végrehajtóhoz tartozó **portot** . Az adott végrehajtóhoz társított munkavégző erőforrásokat is megtekintheti a **gazdagép** oszlopban található hivatkozás kiválasztásával. |
| Hibák | A kiöntő vagy a boltra vonatkozó bármilyen hiba. |

A Storm bolt összefoglaló lapja a következő weboldalhoz hasonlóan néz ki:

![A bolt összefoglaló lapja, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>A topológia figyelése és kezelése a REST API használatával

A Storm felhasználói felülete a REST APIra épül, így a REST API segítségével hasonló felügyeleti és figyelési feladatokat végezhet el. A REST API használatával egyéni eszközöket hozhat létre a Storm-topológiák kezeléséhez és figyeléséhez.

További információ: [Apache Storm felhasználói felület REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Az alábbi információk a REST API Apache Storm HDInsight való használatára vonatkoznak.

> [!IMPORTANT]  
> A Storm REST API nem nyilvánosan elérhető az interneten keresztül. A HDInsight egy SSH-alagúton keresztül kell elérnie a fürt fő csomópontja számára. Az SSH-alagutak létrehozásával és használatával kapcsolatos információkért lásd: az [SSH-alagút használata az Azure HDInsight eléréséhez](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Alap URI

A Linux-alapú HDInsight-fürtökön lévő REST API alap URI-ja az URL-címen érhető el `https://HEADNODEFQDN:8744/api/v1/` , ahol a *HEADNODEFQDN* a fő csomóponttal helyettesíti. A fő csomópont tartományneve a fürt létrehozása során jön létre, és nem statikus.

A fürt fő csomópontjának teljes tartományneve (FQDN) több módon is megkereshető:

| FQDN-felderítési módszer | Description |
| --- | --- |
| SSH-munkamenet | Használja a parancsot `headnode -f` egy SSH-munkamenetből a fürtre. |
| Ambari web | A Ambari-fürt weblapján ( `https://CLUSTERNAME.azurehdinsight.net` ) válassza ki a **szolgáltatások** elemet az oldal tetején, majd válassza a **Storm** lehetőséget. Az **Összefoglalás** lapon válassza a **Storm UI-kiszolgáló** lehetőséget. A Storm felhasználói felületet futtató csomópont teljes tartományneve és REST API a lap tetején jelenik meg. |
| Ambari REST API | A paranccsal `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` információkat kérhet le arról a csomópontról, amelyen a Storm kezelőfelülete és REST API fut. Cserélje le a *CLUSTERNAME* két példányát a fürt nevére. Amikor a rendszer kéri, adja meg a felhasználói (rendszergazdai) fiókhoz tartozó jelszót. A válaszban a JSON-kimenet "host_name" bejegyzése tartalmazza a csomópont teljes tartománynevét. |

### <a name="authentication"></a>Hitelesítés

A REST APIra irányuló kérelmeknek *alapszintű hitelesítést* kell használniuk, ezért a HDInsight-fürthöz tartozó rendszergazdai nevet és jelszót kell használnia.

> [!NOTE]  
> Mivel az egyszerű hitelesítés titkosítatlan szöveggel van ellátva, *mindig* a HTTPS protokollt használja a fürttel folytatott kommunikáció biztonságossá tételéhez.

### <a name="return-values"></a>Visszatérési értékek

A REST API által visszaadott adatok csak a fürtön belül használhatók. A [Apache ZooKeeper](https://zookeeper.apache.org/) -kiszolgálók által visszaadott teljes tartománynév (FQDN) például nem érhető el az internetről.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [fejleszthet Java-alapú topológiákat az Apache Maven használatával](apache-storm-develop-java-topology.md).

A több példás topológiák listáját lásd: [példa Apache Storm topológiák az Azure HDInsight-ben](apache-storm-example-topology.md).
