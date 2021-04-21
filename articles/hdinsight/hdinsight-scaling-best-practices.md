---
title: Fürtméretek méretezése – Azure HDInsight
description: Apache Hadoop-fürt rugalmas skálázása a számítási feladatoknak Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1c388cb070c66fc3a2322c358bc4113ed2106c77
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761848"
---
# <a name="scale-azure-hdinsight-clusters"></a>Fürtök Azure HDInsight méretezése

A HDInsight rugalmasságot biztosít a fürtökben lévő munkavégző csomópontok számának fel- és leskáláihoz rendelkezésre álló lehetőségekkel. Ez a rugalmasság lehetővé teszi a fürtök munkaidő utáni vagy hétvégentei zsugorítását. És bővítse ki az üzleti igények csúcsidőszakában.

Skálázhatja fel a fürtöt az időszakos kötegelt feldolgozás előtt, hogy a fürt megfelelő erőforrásokkal rendelkezik.  Miután a feldolgozás befejeződött, és a használat leállt, skálázja le a HDInsight-fürtöt kevesebb munkavégző csomópontra.

A fürtök skálázhatóak manuálisan az alábbi módszerek egyikével. Az automatikus [skálázási beállításokkal](hdinsight-autoscale-clusters.md) bizonyos metrikákra reagálva automatikusan fel- és leskálásokat is használhat.

> [!NOTE]  
> Csak a HDInsight 3.1.3-as vagy újabb verzióját támogató fürtök támogatottak. Ha nem biztos a fürt verziójában, tekintse meg a Tulajdonságok lapot.

## <a name="utilities-to-scale-clusters"></a>Segédprogramok fürtök méretezéséhez

A Microsoft a következő segédprogramokat biztosítja a fürtök méretezéséhez:

|Segédprogram | Leírás|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure klasszikus parancssori felület](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure Portal](https://portal.azure.com)|Nyissa meg a HDInsight-fürt panelt, válassza a bal oldali menü Fürtméret beállítását, majd a Fürtméret panelen írja be a munkavégző csomópontok számát, és válassza a Mentés lehetőséget. |  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure Portal méretezése beállítás":::

Ezen módszerek bármelyikével percek alatt fel- vagy leskálázhatja a HDInsight-fürtöt.

> [!IMPORTANT]  
> * A klasszikus Azure CLI elavult, és csak a klasszikus üzembe helyezési modellel használható. Minden más üzemelő példányhoz használja az [Azure CLI-t.](/cli/azure/)
> * A PowerShell AzureRM modul elavult.  Amikor csak lehetséges, [használja az Az](/powershell/azure/new-azureps-module-az) modult.

## <a name="impact-of-scaling-operations"></a>A skálázás műveleteinek hatása

Amikor **csomópontokat ad** hozzá a futó HDInsight-fürthöz (felskálás), a feladatokra ez nem lesz hatással. A skálázási folyamat futása közben biztonságosan elküldhet új feladatokat. Ha a skálázás sikertelen, a hiba működőképes állapotba fogja hagyni a fürtöt.

Ha **eltávolítja a** csomópontokat (leskálás), a függőben lévő vagy futó feladatok sikertelenek lesznek a méretezési művelet befejezésekor. Ezt a hibát az okozhatja, hogy egyes szolgáltatások újraindulnak a skálázás során. Előfordulhat, hogy a fürt elakad csökkentett módban a manuális skálázás során.

Az adatcsomópontok számának módosításának hatása a HDInsight által támogatott összes fürttípus esetén eltérő lehet:

* Apache Hadoop

    Zökkenőmentesen növelheti a futó Hadoop-fürtök munkavégző csomópontjainak számát anélkül, hogy ez hatással lenne a feladatokra. Új feladatok is elküldve lesznek, amíg a művelet folyamatban van. A skálázási műveletekben a hibákat a rendszer elegánsan kezeli. A fürt mindig működőképes állapotban marad.

    Ha egy Hadoop-fürtöt kevesebb adatcsomóponttal skáláz le, egyes szolgáltatások újraindulnak. Ez a viselkedés azt okozza, hogy a méretezési művelet befejezésekor az összes futó és függőben lévő feladat meghiúsul. A feladatokat azonban újrakésheti a művelet befejezése után.

* Apache HBase

    Futtatás közben zökkenőmentesen adhat hozzá vagy távolíthat el csomópontokat a HBase-fürtből. A regionális kiszolgálók a skálázás befejezésétől számított néhány percen belül automatikusan kiegyensúlyozottak. A regionális kiszolgálók azonban manuálisan is kiegyensúlyozhatóak. Jelentkezzen be a fürt főcsomópontján, és futtassa a következő parancsokat:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    A HBase-rendszerhéj használatával kapcsolatos további információkért lásd: [Apache HBase-példa használata a HDInsightban](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    A Storm futása közben zökkenőmentesen adhat hozzá vagy távolíthat el adatcsomópontokat. A méretezési művelet sikeres befejezése után azonban újra egyensúlyba kell hoznunk a topológiát. Az újraegyenlözés lehetővé teszi, [](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) hogy a topológia a fürt új csomópontszáma alapján a párhuzamosság beállításait módosítani tudja. A futó topológiák újraegyensúlyozása a következő lehetőségek egyikével lehetséges:

  * A Storm webes felhasználói felülete

    A következő lépésekkel egyensúlyba hoz egy topológiát a Storm felhasználói felületén.

    1. Nyissa `https://CLUSTERNAME.azurehdinsight.net/stormui` meg a böngészőt, ahol `CLUSTERNAME` a a Storm-fürt neve. Ha a rendszer kéri, adja meg a HDInsight-fürt rendszergazdájának (rendszergazdai) nevét és jelszavát, amit a fürt létrehozásakor adott meg.

    1. Válassza ki az újraegyensúlyozáshoz kívánt topológiát, majd kattintson az **Újraegyensúlyozás** gombra. Adja meg az újraegyensúlyozási művelet előtti késleltetést.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="A HDInsight Storm méretezésének újraegyensúlyozása":::

  * Parancssori felületi (CLI) eszköz

    Csatlakozzon a kiszolgálóhoz, és használja a következő parancsot a topológia újraegyensúlyozására:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Olyan paramétereket is megadhat, amelyek felülbírálják az eredetileg a topológia által biztosított párhuzamosságmutatókat. Az alábbi kód például 5 feldolgozói folyamatra, 3 végrehajtóra a kék spout összetevőre és 10 végrehajtóra konfigurálja a topológiát a `mytopology` yellow-bolt összetevő számára.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    A partícióreplikák újraegyensúlyozását a skálázhatósági műveletek után érdemes lehet. További információ: Magas rendelkezésre állású adatok [Apache Kafka HDInsighton.](./kafka/apache-kafka-high-availability.md)

* Apache Hive LLAP

    A feldolgozó csomópontokra való skálázást követően a HDInsight automatikusan beállítja a következő konfigurációkat, `N` és újraindítja a Hive-t.

  * Egyidejű lekérdezések maximális száma: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * A Hive LLAP által használt csomópontok száma: `num_llap_nodes  = N`
  * A Hive LLAP-démon futtatásához futtatott csomópont(k) száma: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Fürtök biztonságos leskálözése

### <a name="scale-down-a-cluster-with-running-jobs"></a>Fürtök leskál való leskálözése futó feladatokkal

Annak érdekében, hogy a futó feladatok ne hibásodnak meg a leskálásos műveletek során, három dolgot kipróbálhat:

1. A fürt leméretezése előtt várja meg, amíg a feladatok befejeződnek.
1. A feladatok manuálisan végződnek.
1. A méretezési művelet befejeztét követően újraküldi a feladatokat.

A függőben lévő és futó feladatok listájának a következő lépésekkel használhatja a YARN **Resource Manager felhasználói** felületét:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  A fürt egy új portállapon nyílik meg.
2. A fő nézetben lépjen a **Fürt** irányítópultok  >  **Ambari kezdőlapjára.** Adja meg a fürt hitelesítő adatait.
3. Az Ambari felhasználói felületén válassza ki a **YARN** elemet a bal oldali menüben található szolgáltatások listájából.  
4. A YARN lapon válassza a **Gyorshivatkozások** lehetőséget, vigye az egérmutatót az aktív főcsomópont fölé, majd válassza **a Resource Manager lehetőséget.**

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache Ambari – gyorshivatkozások Resource Manager felhasználói felülethez":::

A felhasználói felületen közvetlenül Resource Manager a `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` következővel: .

Megjelenik a feladatok listája, valamint azok aktuális állapota. A képernyőképen jelenleg egy feladat fut:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager felhasználói felületi alkalmazások használata":::

A futó alkalmazás manuális futtatásához hajtsa végre a következő parancsot az SSH-rendszerhéjból:

```bash
yarn application -kill <application_id>
```

Például:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Elakadás csökkentett módban

A fürtök leskálázásakor a HDInsight Apache Ambari felügyeleti felületeket használ az extra munkavégző csomópontok leszereléséhez. A csomópontok replikálják a HDFS-blokkokat más online munkavégző csomópontokra. Ezt követően a HDInsight biztonságosan leskálái a fürtöt. A HDFS csökkentett módba kerül a skálázás során. A HDFS-nek ki kell jönnie, amint a skálázás befejeződött. Bizonyos esetekben azonban a HDFS csökkentett módban elakad a méretezési művelet során a fájlblokkok replikációjának alulreplikációja miatt.

Alapértelmezés szerint a HDFS 1-es beállítással van konfigurálva, amely az egyes fájlblokkok `dfs.replication` elérhető példányszámát szabályozza. A fájlblokkok minden példánya a fürt egy másik csomópontján van tárolva.

Ha a blokkmásolatok várt száma nem érhető el, a HDFS csökkentett módba lép, és az Ambari riasztásokat hoz létre. A HDFS csökkentett módba léphet a skálázáshoz. Előfordulhat, hogy a fürt csökkentett módban elakad, ha a rendszer nem észleli a szükséges számú csomópontot a replikációhoz.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Példahibák, ha a csökkentett mód be van kapcsolva

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

A névcsomópont naplóit a mappában, a fürt méretezésének közelében áttekintheti, hogy mikor került `/var/log/hadoop/hdfs/` csökkentett módba. A naplófájlok neve `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Ennek az az oka, hogy a Hive a HDFS ideiglenes fájljaitól függ a lekérdezések futtatása közben. Amikor a HDFS csökkentett módba lép, a Hive nem tud lekérdezéseket futtatni, mert nem tud írni a HDFS-be. A HDFS ideiglenes fájljai az egyes munkavégző csomópont virtuális gépéhez csatlakoztatott helyi meghajtón találhatók. A fájlokat a rendszer három replikán replikálja más munkavégző csomópontok között, legalább.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Hogyan akadályozható meg, hogy a HDInsight csökkentett módban elakadt

Többféleképpen is megakadályozhatja, hogy a HDInsight csökkentett módban legyen:

* A HDInsight leméretezése előtt állítsa le az összes Hive-feladatokat. Másik esetben ütemezheti a leskálás folyamatát, hogy elkerülje a Hive-feladatok futtatásával való ütközést.
* A leméretezés előtt manuálisan tisztítsa meg manuálisan a Hive scratch `tmp` könyvtárfájlját a HDFS-ben.
* A HDInsightot csak három feldolgozó csomópontra skálázható le, minimum. Ne legyen olyan alacsony, mint egy feldolgozó csomópont.
* Ha szükséges, futtassa az parancsot a csökkentett módból való elhagyáshoz.

A következő szakaszok ezeket a lehetőségeket ismertetik.

#### <a name="stop-all-hive-jobs"></a>Az összes Hive-feladat leállítása

Állítsa le az összes Hive-feladatokat, mielőtt leméretezést ad egy feldolgozó csomópontra. Ha a számítási feladat ütemezve van, akkor a Hive-munka után hajtsa végre a horizontális leskálát.

A Hive-feladatok leállítása a skálázás előtt segít minimalizálni a tmp mappában található scratch fájlok számát (ha vannak).

#### <a name="manually-clean-up-hives-scratch-files"></a>A Hive-fájlok manuális törlése

Ha a Hive ideiglenes fájlokat hagyott hátra, manuálisan is megtisztíthatja ezeket a fájlokat a leméretezés előtt a csökkentett mód elkerülése érdekében.

1. A konfigurációs tulajdonság segítségével ellenőrizze, hogy melyik helyet használja a Rendszer a Hive ideiglenes `hive.exec.scratchdir` fájljaihoz. Ez a paraméter a következőn belül van `/etc/hive/conf/hive-site.xml` beállítva:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Állítsa le a Hive-szolgáltatásokat, és győződjön meg arról, hogy minden lekérdezés és feladat befejeződött.

1. Listsa ki a fent található scratch könyvtár tartalmát, hogy lássa, `hdfs://mycluster/tmp/hive/` tartalmaz-e fájlokat:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Itt található egy példa a kimenetre, ha fájlok léteznek:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Ha tudja, hogy a Hive végzett ezekkel a fájlokkal, eltávolíthatja őket. Győződjön meg arról, hogy a Hive nem futtat lekérdezéseket a Yarn felhasználói felületének Resource Manager oldalán.

    Példa parancssor a fájlok HDFS-ről való eltávolítására:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>A HDInsight méretezése három vagy több munkavégző csomópontra

Ha a fürtök gyakran elakadnak csökkentett módban háromnál kevesebb munkavégző csomópontra való leméretezéskor, akkor tartsa meg legalább három munkavégző csomópontot.

Három munkavégző csomóponttal több költséggel jár, mint egyetlen munkavégző csomópontra leméretezés. Ez a művelet azonban megakadályozza, hogy a fürt csökkentett módban elakad.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>A HDInsight leskálása egy feldolgozó csomópontra

A 0 feldolgozó csomópont akkor is fennmarad, ha a fürt egy csomópontra van horizontálisan leskálzva. A 0. feldolgozó csomópontot soha nem lehet leszerelni.

#### <a name="run-the-command-to-leave-safe-mode"></a>A csökkentett módból való elhagyás érdekében futtassa a parancsot

Az utolsó lehetőség a csökkentett mód elhagyó parancsának végrehajtása. Ha a HDFS csökkentett módba került a Hive-fájl replikációja alatt, a következő parancs végrehajtásával hagyhatja el a csökkentett módot:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase-fürt horizontális leskálázata

A régiókiszolgálók skálázása után néhány percen belül automatikusan el lesz kiegyensúlyozott. A régiókiszolgálók manuális kiegyensúlyozához kövesse az alábbi lépéseket:

1. Csatlakozzon a HDInsight-fürthöz SSH használatával. További információ: [SSH használata a HDInsightban.](hdinsight-hadoop-linux-use-ssh-unix.md)

2. Indítsa el a HBase rendszerhéjat:

    ```bash
    hbase shell
    ```

3. A régiókiszolgálók manuális kiegyensúlyozához használja a következő parancsot:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight-fürtök automatikus skálázása](hdinsight-autoscale-clusters.md)

A HDInsight-fürt méretezésére vonatkozó további információkért lásd:

* [Apache Hadoop-fürtök kezelése a HDInsightban a Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Hadoop-fürtök kezelése a HDInsightban az Azure CLI használatával](hdinsight-administer-use-command-line.md#scale-clusters)