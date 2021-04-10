---
title: Fürtök méretének méretezése – Azure HDInsight
description: Apache Hadoop-fürt rugalmas méretezése az Azure HDInsight-beli munkaterhelésnek megfelelően
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 6e6c692e8fc13d1703df44c99e9969ba4db5f119
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872098"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight-fürtök méretezése

A HDInsight rugalmasságot biztosít a fürtökben lévő munkavégző csomópontok számának vertikális felskálázásához és méretezéséhez. Ez a rugalmasság lehetővé teszi, hogy a fürtöket órák vagy hétvégék után is lekicsinyítse. És kiterjesztheti az üzleti igényeknek megfelelően.

A fürt vertikális felskálázása az időszakos kötegelt feldolgozás előtt, hogy a fürt rendelkezik a megfelelő erőforrásokkal.  A feldolgozás befejezése után a használat leáll, a HDInsight-fürt méretét pedig kevesebb munkavégző csomópontra.

Manuálisan is méretezheti a fürtöt az alább vázolt módszerek egyikének használatával. Az automatikus [méretezési](hdinsight-autoscale-clusters.md) beállítások használatával az egyes mérőszámokra adott válasz alapján automatikusan fel-és leskálázást is használhat.

> [!NOTE]  
> Csak a 3.1.3-es vagy újabb verziójú HDInsight-fürtök támogatottak. Ha nem tudja biztosan a fürt verzióját, akkor ellenőrizze a tulajdonságok lapot.

## <a name="utilities-to-scale-clusters"></a>A fürtök méretezését szolgáló segédprogramok

A Microsoft a következő segédprogramokat biztosítja a fürtök méretezéséhez:

|Segédprogram | Leírás|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure klasszikus parancssori felület](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure Portal](https://portal.azure.com)|Nyissa meg a HDInsight-fürt panelt, válassza ki a **fürt méretét** a bal oldali menüben, majd a fürt mérete panelen írja be a munkavégző csomópontok számát, majd kattintson a Mentés gombra.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure Portal méretezési fürt beállítása":::

A módszerek bármelyikével akár percek alatt is méretezheti a HDInsight-fürtöt.

> [!IMPORTANT]  
> * A klasszikus Azure CLI elavult, és csak a klasszikus üzembe helyezési modellel használható. Az összes többi központi telepítéshez használja az [Azure CLI](/cli/azure/)-t.
> * A PowerShell-AzureRM modul elavult.  Ha lehetséges, használja az az [modult](/powershell/azure/new-azureps-module-az) .

## <a name="impact-of-scaling-operations"></a>A skálázási műveletek hatása

Ha csomópontokat **ad hozzá** a futó HDInsight-fürthöz (vertikális felskálázás), a feladatokat nem érinti a rendszer. Az új feladatok biztonságosan elhelyezhetők a skálázási folyamat futása közben. Ha a skálázási művelet meghiúsul, a hiba a fürt működési állapotában marad.

Ha **eltávolítja** a csomópontokat (leskálázás), a függőben lévő vagy futó feladatok sikertelenek lesznek, amikor a skálázási művelet befejeződik. Ezt a hibát a skálázási folyamat során újrainduló szolgáltatások némelyike okozza. Előfordulhat, hogy a fürt egy manuális skálázási művelet során elakad a biztonságos módban.

Az adatcsomópontok számának módosításának következményei a HDInsight által támogatott egyes típusú fürtök esetében változnak:

* Apache Hadoop

    A futó Hadoop-fürtökön lévő munkavégző csomópontok számát a feladatok hatása nélkül is zökkenőmentesen növelheti. A művelet végrehajtása közben új feladatokat is el lehet küldeni. A skálázási művelet hibáit szabályosan kezeli a rendszer. A fürt mindig működőképes állapotban marad.

    Ha egy Hadoop-fürt kevesebb adatcsomóponttal van Lekicsinyítve, egyes szolgáltatások újraindulnak. Ez a viselkedés azt eredményezi, hogy a futó és a függőben lévő feladatok sikertelenek lesznek a skálázási művelet befejezésekor. A művelet befejezését követően azonban újra elküldheti a feladatokat.

* Apache HBase

    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat csomópontokat a HBase-fürthöz. A regionális kiszolgálók a skálázási művelet befejezését követően néhány percen belül automatikusan egyensúlyban vannak. A regionális kiszolgálókat azonban manuálisan is kiegyensúlyozhatja. Jelentkezzen be a fürt átjárócsomóponthoz, és futtassa a következő parancsokat:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    A HBase-rendszerhéj használatával kapcsolatos további információkért tekintse meg az [Apache HBase-példa a HDInsight-ben való használatának első lépéseit](hbase/apache-hbase-tutorial-get-started-linux.md)ismertető témakört.

* Apache Storm

    Az adatcsomópontok zökkenőmentesen is hozzáadhatók vagy eltávolíthatók, amíg a Storm fut. A skálázási művelet sikeres befejezése után azonban újra kell osztania a topológiát. A terheléselosztás lehetővé teszi a topológia számára a [párhuzamossági beállítások](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) újramódosítását a fürtben lévő csomópontok új száma alapján. A futó topológiák újraelosztásához használja az alábbi lehetőségek egyikét:

  * Storm webes felhasználói felület

    A következő lépésekkel kiegyensúlyozhatja a topológiát a Storm felhasználói felületének használatával.

    1. Nyissa meg a `https://CLUSTERNAME.azurehdinsight.net/stormui` webböngészőben, ahol `CLUSTERNAME` a a Storm-fürt neve. Ha a rendszer kéri, adja meg a fürt létrehozásakor megadott HDInsight-Fürtfelügyelő (rendszergazda) nevét és jelszavát.

    1. Válassza ki a megismételni kívánt topológiát, majd kattintson az **újraelosztás** gombra. Adja meg az újraelosztási művelet végrehajtása előtti késleltetést.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight Storm skálázási egyensúly":::

  * Parancssori felület (CLI) eszköz

    Kapcsolódjon a kiszolgálóhoz, és a következő parancs használatával egyenlítse ki a topológiát:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Paramétereket is megadhat a topológia által eredetileg biztosított párhuzamossági javaslatok felülbírálásához. Az alábbi kód például újrakonfigurálja a `mytopology` topológiát 5 munkavégző folyamatra, 3 végrehajtót a kék kiöntő összetevőhöz, és 10 végrehajtót a sárga-bolt összetevőhöz.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    A skálázási műveletek után újra kell osztania a partíciós replikákat. További információ: az [adatok magas rendelkezésre állása Apache Kafka HDInsight](./kafka/apache-kafka-high-availability.md) -dokumentummal.

* Apache Hive LLAP

    A `N` munkavégző csomópontokra való skálázás után a HDInsight automatikusan beállítja a következő konfigurációkat, majd újraindítja a struktúrát.

  * Egyidejű lekérdezések maximális száma: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * A kaptár LLAP által használt csomópontok száma: `num_llap_nodes  = N`
  * A kaptár LLAP démon futtatásához használt csomópont (ok) száma: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Fürt biztonságos méretezése

### <a name="scale-down-a-cluster-with-running-jobs"></a>Fürt leskálázása futó feladatokkal

Ha el szeretné kerülni, hogy a futó feladatok leskálázási művelet közben meghiúsulnak, három dolgot is kipróbálhat:

1. Várjon, amíg a feladatok befejeződik a fürt skálázása előtt.
1. Manuálisan fejezze be a feladatokat.
1. A skálázási művelet megkötése után küldje el újra a feladatokat.

A függőben lévő és futó feladatok listájának megtekintéséhez a következő lépéseket követve használhatja a fonal **Resource Manager felhasználói felületét**:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  A fürt megnyílik egy új portál oldalon.
2. A fő nézetből navigáljon a **fürt irányítópultok**  >  **Ambari kezdőlapra**. Adja meg a fürt hitelesítő adatait.
3. A Ambari felhasználói felületén válassza a **fonal** elemet a szolgáltatások listájában a bal oldali menüben.  
4. A fonal lapon válassza a **gyors hivatkozások** lehetőséget, majd vigye az egérmutatót az aktív fő csomópont fölé, majd válassza a **Resource Manager felhasználói felület** lehetőséget.

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache Ambari – a Resource Manager felhasználói felületének gyors hivatkozásai":::

A Resource Manager felhasználói felületét közvetlenül is elérheti `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Ekkor megjelenik a feladatok listája, valamint a jelenlegi állapotuk. A képernyőképen egy jelenleg futó feladatot futtatunk:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager felhasználói felületi alkalmazások":::

Ha manuálisan szeretné megölni a futó alkalmazást, hajtsa végre a következő parancsot az SSH-rendszerhéjból:

```bash
yarn application -kill <application_id>
```

Például:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Elakadás csökkentett módban

Amikor lekicsinyít egy fürtöt, a HDInsight az Apache Ambari felügyeleti felületét használja az extra feldolgozói csomópontok leszereléséhez. A csomópontok replikálják a HDFS-blokkokat más online munkavégző csomópontokra. Ezt követően a HDInsight biztonságosan méretezi a fürtöt. A skálázási művelet során a HDFS csökkentett módba kerül. A skálázás befejeződése után HDFS kell lennie. Bizonyos esetekben azonban a HDFS a replikálás alatt álló blokkolás miatt egy méretezési művelet során csökkentett módban ragadja meg.

Alapértelmezés szerint az HDFS `dfs.replication` 1 értékkel van konfigurálva, amely azt határozza meg, hogy az egyes állományrendszerek hány példánya érhető el. A rendszer a fájl minden példányát a fürt egy másik csomópontján tárolja.

Ha a blokkoló példányok várt száma nem érhető el, a HDFS biztonságos módba lép, és a Ambari riasztásokat hoz létre. A HDFS csökkentett módba léphetnek a skálázási művelethez. Előfordulhat, hogy a fürt biztonságos módban akad, ha a szükséges számú csomópont nem észlelhető a replikáláshoz.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Hibák például a biztonságos mód bekapcsolásakor

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Tekintse át a csomópontok nevét a `/var/log/hadoop/hdfs/` mappából, a fürt méretezésének időpontja közelében, hogy a rendszer mikor adta meg a biztonságos üzemmódot. A naplófájlok neve `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

A probléma alapvető oka az volt, hogy a struktúra a HDFS ideiglenes fájljaitól függ a lekérdezések futtatása során. Ha a HDFS biztonságos módba lép, a struktúra nem tud lekérdezéseket futtatni, mert nem tud írni a HDFS. A HDFS lévő ideiglenes fájlok az egyes munkavégző csomópont virtuális gépekhez csatlakoztatott helyi meghajtón találhatók. A fájlok replikálása más munkavégző csomópontok között három replikán, minimumon történik.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>A HDInsight letiltása csökkentett módban

A HDInsight csökkentett módban való használatának megakadályozása többféle módon is elvégezhető:

* A HDInsight skálázása előtt állítsa le az összes struktúra-feladatot. Másik lehetőségként ütemezze a skálázási folyamatot, hogy elkerülje a kaptár-feladatok futtatásának ütközését.
* `tmp`A skálázás előtt törölje manuálisan a kaptárt a HDFS.
* Csak három munkavégző csomópontra, minimumra HDInsight le. Ne legyen alacsonyabb, mint egy feldolgozói csomópont.
* Ha szükséges, futtassa a parancsot a biztonságos mód elhagyása érdekében.

A következő szakaszok ezeket a beállításokat ismertetik.

#### <a name="stop-all-hive-jobs"></a>Az összes kaptár-feladat leállítása

Állítsa le az összes kaptár-feladatot, mielőtt egy feldolgozói csomópontra kellene méretezni. Ha a számítási feladat ütemezve van, akkor hajtsa végre a méretezést a struktúra befejezése után.

A struktúra-feladatok skálázása előtt állítsa le a kaptár-feladatokat, így minimálisra csökkenthető a lapozófájlok száma a tmp mappában (ha van ilyen).

#### <a name="manually-clean-up-hives-scratch-files"></a>A kaptár üres fájljainak manuális törlése

Ha a struktúra az ideiglenes fájlok mögött maradt, akkor manuálisan is törölheti ezeket a fájlokat a csökkentett mód elkerülése érdekében.

1. Annak ellenőrzéséhez, hogy a rendszer melyik helyet használja a struktúra ideiglenes fájljaihoz, tekintse meg a `hive.exec.scratchdir` konfigurációs tulajdonságot. Ez a paraméter a következőn belül van beállítva `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Állítsa le a kaptár-szolgáltatásokat, és ellenőrizze, hogy az összes lekérdezés és feladat befejeződött-e.

1. Sorolja fel a fenti, a fentiekben ismertetett Scratch könyvtár tartalmát, `hdfs://mycluster/tmp/hive/` és ellenőrizze, hogy a fájl tartalmazza-e a következő fájlokat:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Íme egy példa kimenet, ha a fájlok léteznek:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Ha ismeri a kaptárt ezekkel a fájlokkal, akkor eltávolíthatja őket. Győződjön meg arról, hogy a kaptár nem rendelkezik futó lekérdezéssel a fonal Resource Manager felhasználói felületének lapján.

    Példa parancssorból a HDFS fájlok eltávolításához:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight skálázása három vagy több munkavégző csomópontra

Ha a fürtök a három munkavégző csomópontnál kevesebbre skálázáskor csökkentett módban ragadnak, akkor legalább három feldolgozó csomópontot kell tartania.

A három feldolgozó csomópont drágább, mint a csak egy feldolgozói csomópontra való skálázás. Ez a művelet azonban megakadályozza, hogy a fürt biztonságos módban ragadjon.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>HDInsight skálázása egy feldolgozói csomópontra

Még akkor is, ha a fürt egy csomópontra van méretezve, a 0. munkavégző csomópont továbbra is fennmarad. A munkavégző csomópont 0 soha nem vonható le.

#### <a name="run-the-command-to-leave-safe-mode"></a>Futtassa a parancsot a biztonságos üzemmód elhagyásához

Az utolsó lehetőség az, hogy végrehajtja a biztonságos mód kihagyása parancsot. Ha a HDFS a-replikáció alatt a biztonsági módba került, a következő parancs futtatásával hagyhatja el a biztonságos üzemmódot:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase-fürt méretezése

A skálázási művelet elvégzése után néhány percen belül automatikusan kiegyenlítettük a régió-kiszolgálókat. A régió-kiszolgálók manuális kiegyensúlyozásához hajtsa végre a következő lépéseket:

1. Kapcsolódjon a HDInsight-fürthöz az SSH használatával. További információ: az [SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A HBase-rendszerhéj elindítása:

    ```bash
    hbase shell
    ```

3. Használja a következő parancsot a régió-kiszolgálók manuális elosztásához:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight-fürtök automatikus skálázása](hdinsight-autoscale-clusters.md)

A HDInsight-fürt méretezésével kapcsolatos részletes információkért lásd:

* [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Hadoop-fürtök kezelése a HDInsight az Azure CLI használatával](hdinsight-administer-use-command-line.md#scale-clusters)