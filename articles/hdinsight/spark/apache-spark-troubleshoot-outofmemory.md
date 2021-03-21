---
title: Működése OutOfMemoryError-kivételek az Azure HDInsight Apache Spark
description: Különböző működése OutOfMemoryError-kivételek Apache Spark-fürthöz az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a15d79f2ae9c3d20a73ec557c57a5c189b18111b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946350"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Működése OutOfMemoryError-kivételek az Azure HDInsight Apache Spark

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Forgatókönyv: működése OutOfMemoryError-kivétel Apache Spark

### <a name="issue"></a>Probléma

A Apache Spark alkalmazás nem tudott kezelni egy működése OutOfMemoryError kezeletlen kivételt. A következőhöz hasonló hibaüzenet jelenhet meg:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Ok

Ennek a kivételnek a legvalószínűbb oka az, hogy nem áll rendelkezésre elég halom memória a Java virtuális gépekhez (JVMs). Ezek a JVMs végrehajtók vagy illesztőprogramokként lesznek elindítva a Apache Spark alkalmazás részeként.

### <a name="resolution"></a>Feloldás

1. Határozza meg a Spark-alkalmazás által kezelendő adatok maximális méretét. Becsülje meg a méretet a bemeneti adatok, a bemeneti adatok átalakításával előállított köztes adatok és a köztes adatok további átalakításával létrehozott kimeneti adatok maximális mérete alapján. Ha a kezdeti becslés nem elegendő, növelje a méretet kis mértékben, és ismételje meg a memória hibáit.

1. Győződjön meg arról, hogy a használni kívánt HDInsight-fürtnek elegendő memória-erőforrás áll a rendelkezésére, és elegendő maggal rendelkezik a Spark-alkalmazás elhelyezéséhez. Ezt úgy határozhatja meg, hogy megtekinti a fürt fonal felhasználói felületének fürt metrikák szakaszát a **felhasznált memória** és a **virtuális mag** **, illetve** a **virtuális mag teljes** száma alapján.

    ![a fonal alapmemóriájának nézete](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Állítsa be a következő Spark-konfigurációkat a megfelelő értékekre. Az alkalmazásra vonatkozó követelmények elosztása a fürt rendelkezésre álló erőforrásaival. Ezek az értékek nem haladhatják meg a rendelkezésre álló memória és magok 90%-át a fonal alapján megtekintve, és meg kell felelnie a Spark-alkalmazás minimális memória-követelményének is:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Az összes végrehajtó által használt memória összesen =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Az illesztőprogram által használt memória összesen =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Forgatókönyv: a Java-halom területének hibája a Apache Spark History-kiszolgáló megnyitására tett kísérlet során

### <a name="issue"></a>Probléma

A Spark History Server eseményeinek megnyitásakor a következő hibaüzenet jelenik meg:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Ok

Ezt a problémát gyakran az erőforrások hiánya okozza a nagyméretű Spark-Event fájlok megnyitásakor. Alapértelmezés szerint a Spark heap mérete 1 GB-ra van beállítva, de a nagyméretű Spark-eseményeknél ennél többre lehet szükség.

Ha ellenőrizni szeretné a betölteni kívánt fájlok méretét, hajtsa végre a következő parancsokat:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Feloldás

A Spark-előzmények kiszolgálójának memóriáját a `SPARK_DAEMON_MEMORY` Spark-konfigurációban található tulajdonság szerkesztésével és az összes szolgáltatás újraindításával növelheti.

Ezt a Ambari böngésző felhasználói felületén végezheti el a Spark2/config/Advanced Spark2-env szakasz kiválasztásával.

![Speciális spark2 – env szakasz](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Adja hozzá a következő tulajdonságot a Spark History-kiszolgáló memóriájának a 1g-ről 4G-re való módosításához: `SPARK_DAEMON_MEMORY=4g` .

![Spark-tulajdonság](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Győződjön meg arról, hogy az összes érintett szolgáltatást újraindítja a Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Forgatókönyv: a Livy-kiszolgáló nem indul el Apache Spark fürtön

### <a name="issue"></a>Probléma

A Livy-kiszolgáló nem indítható el egy Apache Sparkon [(Spark 2,1 Linuxon (HDI 3,6)]. Kísérlet az eredmények újraindítására a következő Livy-naplókból:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Ok

`java.lang.OutOfMemoryError: unable to create new native thread` kiemeli, hogy az operációs rendszer nem tud több natív szálat hozzárendelni a JVMs. Megerősítette, hogy ezt a kivételt a folyamaton belüli szálak számának korlátja okozza.

Ha a Livy-kiszolgáló váratlanul leáll, a Spark-fürtökkel létesített összes kapcsolat leáll, ami azt jelenti, hogy az összes feladat és kapcsolódó adat el fog veszni. A HDP 2,6 munkamenet-helyreállítási mechanizmus bevezetésekor a Livy a Zookeeper-ben tárolja a munkamenet részleteit a Livy-kiszolgáló újbóli helyreállítása után.

Ha nagy számú feladatot küldenek el a Livy-on keresztül, a Livy-kiszolgáló magas rendelkezésre állásának részeként a rendszer a ZK (HDInsight-fürtökön) tárolja ezeket a munkamenet-állapotokat, és helyreállítja ezeket a munkameneteket a Livy szolgáltatás újraindításakor. A váratlan megszakítás utáni újraindításkor a Livy egy szálat hoz létre egy munkamenetben, és ez egy bizonyos számú, a-helyreállított munkamenetet generál, ami túl sok szálat hoz létre.

### <a name="resolution"></a>Feloldás

Törölje az összes bejegyzést az alábbi lépésekkel.

1. A Zookeeper-csomópontok IP-címének lekérése a használatával

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. A fenti parancs a saját fürt összes zookeeperek listázott

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. A Zookeeper-csomópontok összes IP-címének lekérése a ping paranccsal, vagy a átjárócsomóponthoz Zookeeper való kapcsolódás a ZK név használatával

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Ha csatlakozott a Zookeeper-hez, hajtsa végre a következő parancsot az összes újraindításra megkísérelt munkamenet listázásához.

    1. A legtöbb esetben ez több mint 8000 munkamenetből álló lista lehet. ####

        ```bash
        ls /livy/v1/batch
        ```

    1. A következő parancs az összes helyreállítható munkamenet eltávolítása. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Várjon, amíg a fenti parancs befejeződik, és a kurzor visszaadja a kérdést, majd újraindítja a Livy szolgáltatást a Ambari-ból, amelynek sikeresnek kell lennie.

> [!NOTE]
> `DELETE` a Livy munkamenet befejezése után a végrehajtás befejeződött. A Livy batch-munkamenetek nem törlődnek automatikusan, amint a Spark-alkalmazás befejeződik, ami a tervezés szerint történik. A Livy-munkamenetek egy POST-kérelemmel létrehozott entitások a Livy Rest Serveren. Az `DELETE` entitás törléséhez hívás szükséges. Vagy várnia kell a GC beindítását.

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* A [Spark memória-kezelési áttekintése](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [A Spark-alkalmazás hibakeresése a HDInsight-fürtökön](/archive/blogs/azuredatalake/spark-debugging-101).

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.