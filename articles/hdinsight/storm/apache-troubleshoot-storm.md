---
title: Storm-hibák az Azure HDInsight használatával
description: Választ kaphat a Apache Storm Azure HDInsight való használatával kapcsolatos gyakori kérdésekre.
keywords: Azure HDInsight, Storm, GYIK, hibaelhárítási útmutató, gyakori problémák
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946317"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Apache Storm az Azure HDInsight használatával – problémamegoldás

Ismerje meg a leggyakoribb problémákat és azok megoldásait [Apache Storm](https://storm.apache.org/) hasznos adatok [Apache Ambari](https://ambari.apache.org/)való használatához.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hogyan hozzáférni a Storm felhasználói felületéhez a fürtön?

A Storm felhasználói felületének böngészőből való eléréséhez két lehetőség közül választhat:

### <a name="apache-ambari-ui"></a>Apache Ambari felhasználói felület

1. Nyissa meg a Ambari irányítópultot.
2. A szolgáltatások listájában válassza a **Storm** elemet.
3. A **gyors hivatkozások** menüben válassza a **Storm UI** lehetőséget.

### <a name="direct-link"></a>Közvetlen hivatkozás

A Storm felhasználói felületét a következő URL-címen érheti el:

`https://<cluster DNS name>/stormui`

Például: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hogyan a Storm Event hub kiöntő ellenőrzőpont-információit az egyik topológiáról a másikra?

Ha az Azure Event Hubsból beolvasott topológiákat fejleszt az HDInsight Storm Event hub kiöntő. jar fájljával, olyan topológiát kell üzembe helyeznie, amelynek neve megegyezik egy új fürt nevével. Azonban meg kell őriznie a régi fürtön [Apache ZooKeeperhoz](https://zookeeper.apache.org/) véglegesített ellenőrzőpont-adatellenőrzéseket.

### <a name="where-checkpoint-data-is-stored"></a>Ellenőrzőpont-adattárolók

Az eltolások ellenőrzőpont-értékeit az Event hub kiöntő ZooKeeper tárolja két fő elérési úton:

- A nem tranzakciós kiöntő ellenőrzőpontok a ben tárolódnak `/eventhubspout` .

- A tranzakciós kiöntő ellenőrzőpont-adatkészletek tárolása a-ben történik `/transactional` .

### <a name="how-to-restore"></a>Visszaállítás

A ZooKeeper-ből történő adatexportáláshoz használt parancsfájlok és kódtárak beszerzéséhez, majd az ZooKeeper új névvel történő importálásához tekintse meg a [HDInsight Storm-példák](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)című témakört.

A lib mappa. jar fájlokkal rendelkezik, amelyek tartalmazzák az exportálási/importálási művelet megvalósítását. A bash mappa egy példa parancsfájlt tartalmaz, amely bemutatja, hogyan exportálhatja az adatok a régi fürtön lévő ZooKeeper-kiszolgálóról, majd importálhatja a ZooKeeper-kiszolgálóra az új fürtön.

Futtassa a [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) parancsfájlt a ZooKeeper-csomópontokból az exportáláshoz, majd az adatok importálásához. Frissítse a parancsfájlt a megfelelő Hortonworks-adatplatform (HDP) verzióra. (Dolgozunk a parancsfájlok általános HDInsight való végrehajtásán. Az általános parancsfájlok a fürt bármely csomópontján futtathatók a felhasználó módosítása nélkül.)

Az export parancs a metaadatokat egy Apache Hadoop elosztott fájlrendszer (HDFS) elérési útra írja (az Azure Blob Storage vagy a Azure Data Lake Storage) egy megadott helyen.

### <a name="examples"></a>Példák

#### <a name="export-offset-metadata"></a>Eltolási metaadatok exportálása

1. Az SSH használatával nyissa meg a ZooKeeper-fürtöt azon a fürtön, amelyről az ellenőrzőpont-eltolást exportálni kívánja.
2. Futtassa a következő parancsot (miután frissítette a HDP-verzió sztringjét), hogy exportálja a ZooKeeper eltolási értékeit a `/stormmetadta/zkdata` HDFS elérési útjába:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Eltolási Metaadatok importálása

1. Az SSH használatával nyissa meg a ZooKeeper-fürtöt azon a fürtön, amelyről az ellenőrzőpont-eltolást importálni kell.
2. Futtassa a következő parancsot (miután frissítette a HDP-verzió sztringjét), hogy az ZooKeeper-eltolási adatok importálása a HDFS elérési útjáról `/stormmetadata/zkdata` a cél fürt ZooKeeper-kiszolgálójára:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Eltolási metaadatok törlése, hogy a topológiák el tudják kezdeni az adatok feldolgozását az elejéről, vagy a felhasználó által választott időbélyegből

1. Az SSH használatával nyissa meg a ZooKeeper-fürtöt azon a fürtön, amelyről az ellenőrzőpont-eltolást törölni kell.
2. Futtassa a következő parancsot (miután frissítette a HDP-verzió sztringjét) az aktuális fürt összes ZooKeeper-eltolási értékének törléséhez:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hogyan megkeresni a Storm bináris fájljait egy fürtön?

Az aktuális HDP-verem Storm bináris fájljai a következőben találhatók: `/usr/hdp/current/storm-client` . A hely ugyanaz, mint a fő csomópontok és a munkavégző csomópontok esetében.

Több bináris fájl is lehet a/usr/HDP adott HDP-verzióihoz (például `/usr/hdp/2.5.0.1233/storm` ). A `/usr/hdp/current/storm-client` mappa a fürtön futó legújabb verzióra van összekapcsolva.

További információ: [Kapcsolódás HDInsight-fürthöz SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) és [Apache Storm](https://storm.apache.org/)használatával.

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hogyan megállapítani egy Storm-fürt telepítési topológiáját?

Először azonosítsa a HDInsight Storm-mel telepített összes összetevőt. A Storm-fürtök négy csomópontos kategóriából állnak:

* Átjáró-csomópontok
* Fő csomópontok
* ZooKeeper-csomópontok
* Munkavégző csomópontok

### <a name="gateway-nodes"></a>Átjáró-csomópontok

Az átjáró-csomópont egy átjáró és fordított proxy szolgáltatás, amely lehetővé teszi a nyilvános hozzáférést egy aktív Ambari-kezelési szolgáltatáshoz. Emellett kezeli a Ambari Leader-választásokat is.

### <a name="head-nodes"></a>Fő csomópontok

A Storm-fej csomópontjai a következő szolgáltatásokat futtatják:
* Nimbus
* Ambari-kiszolgáló
* Ambari metrikák kiszolgálója
* Ambari metrikák gyűjtője
 
### <a name="zookeeper-nodes"></a>ZooKeeper-csomópontok

A HDInsight három csomópontos ZooKeeper kvórumot tartalmaz. A kvórum mérete rögzített, és nem konfigurálható újra.

A fürt Storm Services a ZooKeeper kvórum automatikus használatára van konfigurálva.

### <a name="worker-nodes"></a>Munkavégző csomópontok

A Storm Worker csomópontjai a következő szolgáltatásokat futtatják:
* Felügyelő
* Worker Java Virtual Machines (JVMs) a futó topológiák futtatásához
* Ambari-ügynök

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hogyan megkeresi a Storm Event hub kiöntő bináris fájljait a fejlesztéshez?

A Storm Event hub kiöntő. jar fájloknak a topológiával való használatával kapcsolatos további információkért tekintse meg a következő forrásokat.

### <a name="java-based-topology"></a>Java-alapú topológia

[Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (Java) szolgáltatással](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-alapú topológia (mono on HDInsight 3.4 + Linux Storm-fürtök)

[Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (C#) használatával](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>A legújabb Apache Storm Event hub kiöntő bináris fájljai a HDInsight 3.5 + Linux Storm-fürtökhöz

Ha szeretné megtudni, hogyan használhatja a HDInsight 3.5 + Linux Storm-fürtöket használó legújabb Storm Event hub kiöntőt, tekintse meg a [MVN-tárház információs fájlját](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Példák a forráskódra

Tekintse át a [példákat](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) az Azure Event hub beolvasására és írására egy Azure HDInsight-fürtön található Apache Storm-topológia használatával (Java-ban írt).

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hogyan megkeresni a Storm Log4J 2 konfigurációs fájlját a fürtökön?

A Storm Services [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) konfigurációs fájljainak azonosításához.

### <a name="on-head-nodes"></a>A fő csomópontokon

A rendszer beolvassa a Nimbus Log4J konfigurációját `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>Munkavégző csomópontokon

A felügyelő Log4J-konfigurációjának beolvasása `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

A Worker Log4J konfigurációs fájljának olvasása: `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

Példák `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Nem vezető kivétel

A topológia elküldésekor a felhasználó a következőhöz hasonló hibaüzenetet kaphat: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

A megoldáshoz előfordulhat, hogy a felhasználónak be kell állítania egy jegyet, hogy a csomópontok újraindulnak vagy újraindulnak. További információ: [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.