---
title: Üzembe helyezés és kezelés az Azure HDInsight az Apache Storm-topológiák
description: Ismerje meg, hogyan helyezheti üzembe, figyelheti és kezelheti a Storm irányítópultjának használata a HDInsight Linux-alapú Apache Storm-topológiák. Hadoop tools for Visual Studio használata.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 8b27ad34bdc6fcbd7a1eb46515fbf33c96d02528
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682897"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Üzembe helyezés és kezelés az Azure HDInsight az Apache Storm-topológiák 

Ebben a dokumentumban megismerkedhet a kezeléséhez és monitorozásához [Apache Storm](https://storm.apache.org/) Storm HDInsight-fürtökön futó topológiákat.

> [!IMPORTANT]  
> A jelen cikkben ismertetett lépések a Linux-alapú Storm on HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>


## <a name="prerequisites"></a>Előfeltételek

* **Egy Linux-alapú Storm on HDInsight-fürt**: lásd: [Apache Storm on HDInsight – első lépések](apache-storm-tutorial-get-started-linux.md) fürt létrehozásának lépései

* (Nem kötelező) **SSH és SCP-ismeretek**: További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Nem kötelező) **a visual Studio**: Az Azure SDK 2.5.1-es vagy újabb verzió és a Data Lake Tools for Visual Studio. További információkért lásd: [Ismerkedés a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    A Visual Studio következő verzióinak valamelyike:

  * A Visual Studio 2012 Update 4

  * A Visual Studio 2013 Update 4 vagy [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (any edition)

  * Visual Studio 2017 (bármely kiadás). A Data Lake Tools for Visual Studio 2017 Azure workload munkafolyamatának részét vannak telepítve.

## <a name="submit-a-topology-visual-studio"></a>A topológia beküldése: Visual Studio

A HDInsight Tools használható a C# vagy hibrid topológiák a Storm fürthöz való elküldéséhez. Az alábbi lépéseket egy minta alkalmazást kell használni. A HDInsight Tools használatával létrehozásával kapcsolatos információkért lásd: [C#-topológiák fejlesztése a HDInsight Tools for Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md).

1. Ha Ön még nem telepítette a legújabb verzióját a Data Lake tools for Visual Studio, [Ismerkedés a Data Lake Tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > A Data Lake Tools for Visual Studio korábban is nevezett a HDInsight Tools for Visual Studio.
    >
    > A Data Lake Tools for Visual Studio szerepelnek a __Azure számítási feladata__ Pro Visual Studio 2017.

2. Nyissa meg a Visual Studióban, válassza ki **fájl** > **új** > **projekt**.

3. Az a **új projekt** párbeszédpanelen bontsa ki **telepített** > **sablonok**, majd válassza ki **HDInsight**. A sablonok listájából válassza **Storm minta**. A párbeszédpanel alján írja be az alkalmazás nevét.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és válassza ki **Küldés a HDInsight alatt futó Stormmal**.

   > [!NOTE]  
   > Ha a rendszer kéri, adja meg a bejelentkezési hitelesítő adatokat az Azure-előfizetésében. Ha több előfizetéssel rendelkezik, jelentkezzen be a Storm on HDInsight-fürt tartalmazza.

5. Válassza ki a Storm, a HDInsight-fürtön a **Storm-fürt** legördülő listából válassza ki, és válassza ki **küldés**. -E a küldés sikeres használatával figyelheti a **kimeneti** ablak.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>A topológia beküldése: Az SSH és a Storm-parancs

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Cserélje le **felhasználónév** az SSH-bejelentkezési nevét. Cserélje le **CLUSTERNAME** a HDInsight-fürt nevére:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    További információ az SSH-val a HDInsight-fürthöz való csatlakozáshoz: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Használja az alábbi parancsot példatopológia indításához:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Ez a parancs elindítja a fürtön a WordCount példatopológiát. Ez a topológia véletlenszerűen állít elő mondatokat, és ezután a a mondatok egyes szavak előfordulását számolja.

   > [!NOTE]  
   > A topológia a fürtre történő elküldésekor a fürtöt tartalmazó jar-fájlt a `storm` parancs használata előtt kell másolnia. A fájl másolása a fürthöz, használhatja a `scp` parancsot. Például: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > A WordCount-példa és egyéb Storm Starter-példák megtalálhatóak a fürtön a következő helyen: `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Küldje el a topológia: programozott módon

A topológia a Nimbus szolgáltatás használatával programozott módon telepítheti. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) Itt egy példa azt mutatja be, hogyan helyezheti üzembe, és indítsa el a topológia a Nimbus szolgáltatással Java-alkalmazás.

## <a name="monitor-and-manage-visual-studio"></a>Figyelheti és kezelheti: Visual Studio

A topológia elküldésekor a Visual studióval, a **Storm-topológiák** nézet jelenik meg. Válassza ki a topológiát a működő topológia kapcsolatos információk megtekintéséhez a listából.

![a Visual studio-figyelő](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Megtekintheti továbbá **Storm-topológiák** a **Server Explorer** kibontásával **Azure** > **HDInsight**, majd Kattintson a jobb gombbal a Storm on HDInsight-fürtöt, majd válassza ki **Zobrazit Topologie Stormu**.

Válassza ki az alakzatot a spoutok vagy boltok ezeket az összetevőket kapcsolatos információk megtekintéséhez. Egy új ablakban nyílik meg minden kijelölt elemek.

### <a name="deactivate-and-reactivate"></a>Inaktiválás és újraaktiválása

A topológia inaktiválása felfüggeszti, leállított, vagy újra aktiválni. Ezek a műveletek végrehajtásához használja a __inaktiválás__ és __újraaktiválása__ gombok felső részén a __topológia összegzése__.

### <a name="rebalance"></a>Újraegyensúlyozása

A topológia újraegyensúlyozása lehetővé teszi a rendszer vizsgálja felül a topológia párhuzamosságát. Például ha a fürt további megjegyzések hozzáadásának méretezett át, újraegyensúlyozása lehetővé teszi a topológiában az új csomópontok megtekintéséhez.

A topológia újraegyensúlyozására, használja a __Újraegyensúlyozására__ gombot a felső részén a __topológia összegzése__.

> [!WARNING]  
> A topológia újraegyensúlyozása először inaktiválja a topológiát, feldolgozók egyenletesen újraterjeszti a fürtön, majd végül adja vissza a topológia újraegyensúlyozása történt előtti állapotra. Tehát a topológia volt aktív, ha válik aktívvá újra. Ha inaktiválva lett, inaktív marad.

### <a name="kill-a-topology"></a>A topológia leállítása

Storm-topológiák továbbra is fut, le vannak állítva, vagy a fürt törlésekor. A topológia leállításához használja a __Kill__ gombot a felső részén a __topológia összegzése__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Figyelheti és kezelheti: Az SSH és a Storm-parancs

A `storm` segédprogram lehetővé teszi, hogy a futó topológiák a parancssorból. Használat `storm -h` parancsok teljes listáját.

### <a name="list-topologies"></a>Lista topológiák

Használja az alábbi parancsot az összes futó topológiák:

    storm list

Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Inaktiválás és újraaktiválása

A topológia inaktiválása felfüggeszti, leállított, vagy újra aktiválni. A következő paranccsal inaktiválása, és aktiválja újra:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>A futó topológiát Kill

Storm-topológiákat, miután elindult, továbbra is fut, amíg kilép. A topológia leállításához használja a következő parancsot:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Újraegyensúlyozása

A topológia újraegyensúlyozása lehetővé teszi a rendszer vizsgálja felül a topológia párhuzamosságát. Például ha a fürt további megjegyzések hozzáadásának méretezett át, újraegyensúlyozása lehetővé teszi a topológiában az új csomópontok megtekintéséhez.

> [!WARNING]  
> A topológia újraegyensúlyozása először inaktiválja a topológiát, feldolgozók egyenletesen újraterjeszti a fürtön, majd végül adja vissza a topológia újraegyensúlyozása történt előtti állapotra. Tehát a topológia volt aktív, ha válik aktívvá újra. Ha inaktiválva lett, inaktív marad.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Figyelheti és kezelheti: A Storm felhasználói felület

A HDInsight-fürtön elérhető Storm webes felhasználói felületet biztosít a futó topológiákkal való munkavégzéshez. A Storm felhasználói felületének megjelenítéséhez egy webböngészőben segítségével nyissa meg **https://CLUSTERNAME.azurehdinsight.net/stormui**, ahol **CLUSTERNAME** a fürt neve.

> [!NOTE]  
> Ha a rendszer felkéri a felhasználónév és a jelszó megadására, a fürt létrehozásakor használt fürtrendszergazda (rendszergazda) nevét és jelszavát adja meg.

### <a name="main-page"></a>Fő lapján

A fő lapján, a Storm felhasználói felülete a következő információkat biztosítja:

* **Fürt összegzése**: A Storm-fürt alapvető adatait.
* **Összefoglaló topológia**: Futó topológiák listája. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott topológiák további információt.
* **Összefoglaló felügyelő**: A Storm felügyelő kapcsolatos információk.
* **Nimbus-konfiguráció**: Nimbus a fürt konfigurációját.

### <a name="topology-summary"></a>A topológia összegzése

A hivatkozás kiválasztása a **topológia összegzése** szakasz jelenít meg a topológia a következő információkat:

* **Összefoglaló topológia**: A topológia alapvető adatait.
* **Topológia műveletek**: A topológia elvégezhető felügyeleti műveleteket.

  * **Aktiválja**: Folytatja az inaktivált topológia feldolgozásra.
  * **Inaktiválás**: Megszakítja a futó topológiát.
  * **Újraegyensúlyozására**: Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Ez a művelet lehetővé teszi, hogy a topológia párhuzamosságának kompenzálják a fürtben található csomópontok száma nagyobb vagy csökkent.

    További információkért lásd: <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">ismertetése az Apache Storm-topológia párhuzamosságát</a>.
  * **Kill**: Storm-topológia leállítása időtúllépést követően.
* **Topológiastatisztikák**: A topológia statisztikája. Az oldalon a többieknek időkeretét beállításához használja a hivatkozásokat a **ablak** oszlop.
* **Spoutok**: A különböző a topológia használják. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott tartalmaz a spoutokkal kapcsolatban további információt.
* **Boltok**: A boltok a topológia használják. Ebben a szakaszban a hivatkozások segítségével megtekintheti az adott tartalmaz a boltokkal kapcsolatban további információt.
* **Topológiakonfiguráció**: A konfiguráció a kiválasztott topológia.

### <a name="spout-and-bolt-summary"></a>Spout és Bolt összegzése

Egy a spout kiválasztása a **Spoutok** vagy **boltok** szakaszok megjeleníti a kiválasztott elem a következő információkat:

* **Összetevő összefoglalás**: A spout vagy bolt alapvető adatait.
* **Spout vagy Bolt-statisztikák**: A spout vagy bolt statisztikája. Az oldalon a többieknek időkeretét beállításához használja a hivatkozásokat a **ablak** oszlop.
* **Beviteli statisztikák** (csak boltok esetében): A bemeneti streamekhez a bolt által felhasznált információk.
* **Kimeneti statisztikák**: A spout vagy bolt által kibocsátott adatfolyamok kapcsolatos információk.
* **Végrehajtóval**: A példányok a spout vagy bolt kapcsolatos információk. Válassza ki a **Port** diagnosztikai adatok megtekintését, egy adott végrehajtó bejegyzés előállítása a ezen a példányon.
* **Hibák**: Hiba történt a spout vagy bolt információt.

## <a name="monitor-and-manage-rest-api"></a>Figyelheti és kezelheti: REST API

A Storm felhasználói felülete a REST API-t épül, így hasonló felügyeleti és figyelési funkcióit, a REST API használatával is elvégezheti. A REST API használatával hozhat létre egyéni kezeléséhez és monitorozásához Storm-topológiák.

További információkért lásd: [Apache Storm UI REST API-val](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Az alábbi adatokat csak a REST API-val, az Apache Storm on HDInsight.

> [!IMPORTANT]  
> A Storm – REST API-t nem érhető el nyilvánosan az interneten keresztül, és a egy SSH-alagutat a HDInsight-fürt fő csomópontjának használatával érhetők el. Létrehozásával és SSH-alagút használatával kapcsolatos információkért lásd: [használata SSH-bújtatással való eléréséről az Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Apache Oozie és egyéb webes](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Alap URI-t

Az alap URI a REST API-hoz a Linux-alapú HDInsight-fürtökön érhető el, az átjárócsomópont **https:\//HEADNODEFQDN:8744/api/v1/**. A tartománynév, a fő csomópont jön létre a fürt létrehozásakor, és nem statikus.

A fürt fő csomópontjának teljes tartománynevét (FQDN) a különféle módokon találja:

* **SSH-munkamenetből történő**: A parancs használata `headnode -f` a fürthöz egy SSH-munkamenetből.
* **Az Ambari webes**: Válassza ki **szolgáltatások** lehetőséget az oldal tetején, majd válassza ki **Storm**. Az a **összefoglalás** lapon jelölje be **Storm felhasználói felület Server**. A csomópont, a Storm felhasználói felület és a REST API-t üzemeltető teljes Tartománynevét, amely a lap tetején jelenik meg.
* **Az Ambari REST API-ból**: A parancs használata `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` kérhet le információt a csomópontot, a Storm felhasználói felület és a REST API-t futtat. Cserélje le **CLUSTERNAME** a fürt nevére. Amikor a rendszer kéri, adja meg a bejelentkezési (rendszergazdai) fiókjának jelszavát. A válasz a "gazdaszámítógép_neve" bejegyzés tartalmazza a csomópont teljes Tartományneve.

### <a name="authentication"></a>Hitelesítés

A REST API-kérésnek kell használnia **alapszintű hitelesítés**, így a HDInsight fürt rendszergazdai felhasználónevét és jelszavát használja.

> [!NOTE]  
> Alapszintű hitelesítést a rendszer tiszta szöveges használatával küldi el, mert kell **mindig** HTTPS használatával biztonságos kommunikáció a fürtöt.

### <a name="return-values"></a>Visszatérési értékek

Lehet, hogy a REST API-tól visszaadott információk csak a fürtön belül használható. Ha például a teljesen minősített tartománynevét (FQDN) a visszaadott [Apache ZooKeeper](https://zookeeper.apache.org/) kiszolgáló nem érhető el az internetről.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fejlesztés Java-alapú topológiák az Apache Maven segítségével](apache-storm-develop-java-topology.md).

További példa topológiák listáját lásd: [a HDInsight Apache Storm példatopológiái](apache-storm-example-topology.md).
