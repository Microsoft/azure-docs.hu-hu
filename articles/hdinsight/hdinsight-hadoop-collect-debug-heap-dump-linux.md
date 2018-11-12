---
title: Az Apache Hadoop-szolgáltatásokhoz a HDInsight - Azure halomürítések engedélyezése
description: Halomürítések engedélyezése a Hibakeresés és elemzés Linux-alapú HDInsight-fürtök az Apache Hadoop-szolgáltatásokhoz.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 966f05fba96cc829c3a11331e2a66609705f6f4f
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037687"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>A Linux-alapú HDInsight az Apache Hadoop-szolgáltatásokhoz halomürítések engedélyezése

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Halomürítések az alkalmazás memória, beleértve a változók értékeit, a memóriakép létrehozásakor pillanatképet tartalmaz. Ezért hasznosak lehetnek, amelyet a futásidejű kapcsolatos problémák diagnosztizálásához.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések Linux használó HDInsight-fürtök csak dolgozhat. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Services

A következő szolgáltatásokat halomürítések engedélyezheti:

* **Az Apache hcatalog** -tempelton
* **Az Apache hive** -hiveserver2-n, metaadattár, derbyserver
* **a mapreduce** -jobhistoryserver
* **Az Apache yarn** -resourcemanager, nodemanager, timelineserver
* **Az Apache hdfs** -datanode, secondarynamenode, namenode

Halomürítések engedélyezése a térkép a és is csökkentheti HDInsight által futtatott folyamatok.

## <a name="configuration"></a>Halommemória-memóriakép konfigurációs ismertetése

Halomürítések engedélyezve vannak a beállítások átadásával (mellett dönt, más néven vagy paraméterek), a JVM-szolgáltatás indításakor. A legtöbb Hadoop-szolgáltatásokhoz módosíthatja a át ezeket a beállításokat a szolgáltatás elindításához használt PowerShell-parancsfájlt.

Minden parancsprogramhoz van egy exportálásának  **\* \_OPTS**, amely tartalmazza a JVM át a beállításokat. Például a **hadoop-env.sh** szkriptet, a sor kezdődő `export HADOOP_NAMENODE_OPTS=` tartalmazza, a NameNode szolgáltatás beállításait.

Képezze le, és csökkentse folyamatok kissé eltérő, mivel ezek a műveletek egyik gyermekfolyamata a MapReduce szolgáltatást. Minden egyes leképezése, vagy csökkentse egy gyermek tárolóban fut, a folyamat, és két olyan bejegyzéseket, amelyek tartalmazzák a JVM lehetőségeket vannak. Mindkét szereplő **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Javasoljuk az Ambari az Apache Ambari leíró módosítások replikálása a fürtben található csomópontok között, a parancsfájlok és a mapred-site.xml beállítások módosítása. Tekintse meg a [az Ambari használatával](#using-ambari) szakasz lépéseit.

### <a name="enable-heap-dumps"></a>Halomürítések engedélyezése

A következő beállítás lehetővé teszi, hogy halomürítések egy OutOfMemoryError esetén:

    -XX:+HeapDumpOnOutOfMemoryError

A **+** azt jelzi, hogy ez a beállítás engedélyezve van. Ez a beállítás alapértelmezés szerint le van tiltva.

> [!WARNING]
> Halomürítések vannak a HDInsight a Hadoop-szolgáltatásokhoz alapértelmezés szerint nincs engedélyezve, mivel lehet, hogy nagy a memóriaképeket. Ha engedélyezi a számukra hibaelhárítási, ne felejtse el reprodukálni a hibát és a memóriaképek összegyűjtése után tiltsa le azokat.

### <a name="dump-location"></a>Memóriakép helye

A memóriakép-fájl alapértelmezett helye az aktuális munkakönyvtár. Szabályozhatja, ahol a fájlt tárolja a következő kapcsoló használatával:

    -XX:HeapDumpPath=/path

Például `-XX:HeapDumpPath=/tmp` hatására a memóriaképek könyvtárban kell tárolni.

### <a name="scripts"></a>Parancsprogramok

Egy parancsfájlt is indíthat amikor egy **OutOfMemoryError** történik. Például riasztást kiváltó értesítést, hogy tudja, hogy a hiba történt. Használja a következő parancsfájl eseményindítás egy __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Mivel a Hadoop elosztott rendszerek, minden más használt parancsfájl, amely a szolgáltatás fut a fürt minden csomópontján kell elhelyezni.
> 
> A parancsfájl kell is lehet a szolgáltatás fut, és meg kell adnia a fiók által elérhető helyen végrehajtási engedélyeket. Például előfordulhat, hogy szeretné tárolni a parancsfájlok `/usr/local/bin` és `chmod go+rx /usr/local/bin/filename.sh` adjon olvasási és végrehajtási engedélyeket.

## <a name="using-ambari"></a>Az Ambari használatával

Szolgáltatás konfigurációjának módosításához használja az alábbi lépéseket:

1. Nyissa meg az Ambari webes felhasználói felület a fürt számára. Az URL-cím https://YOURCLUSTERNAME.azurehdinsight.net.

    Amikor a rendszer kéri, a webhely a HTTP-fiók használatával hitelesíteni (alapértelmezett: rendszergazdai) és a fürthöz tartozó jelszót.

   > [!NOTE]
   > Kérheti egy második alkalommal Ambari által a felhasználónevet és jelszót. Ha igen, adja meg az azonos fióknevet és jelszót

2. Használatával listája a bal oldalon található, válassza ki a módosítani kívánt szolgáltatás területen. Ha például **HDFS**. A központ területen válassza ki a **Configs** fülre.

    ![Az Ambari webes kiválasztott HDFS Configs lap képe](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Használatával a **szűrése...**  bejegyzést, adja meg **jóváhagyja**. Csak ezt a szöveget tartalmazó elemek jelennek meg.

    ![Szűrt lista](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Keresse meg a  **\* \_OPTS** bejegyzést a szolgáltatás kívánt halomürítések engedélyezése a, és adja hozzá az engedélyezni kívánt beállításokat. Az alábbi képen hozzá `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` , a **HADOOP\_NAMENODE\_OPTS** bejegyzés:

    ![-XX-HADOOP_NAMENODE_OPTS: + HeapDumpOnOutOfMemoryError - XX: HeapDumpPath = / tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Amikor halommemória engedélyezése a térkép listázása, vagy csökkentse gyermekfolyamata, keresse meg a mezők nevű **mapreduce.admin.map.child.java.opts** és **mapreduce.admin.reduce.child.java.opts**.

    Használja a **mentése** gombra kattintva mentse a módosításokat. A módosításokat leíró rövid megjegyzés is megadhatja.

5. A módosítások alkalmazása után a **újraindítás szükséges** ikon jelenik meg egy vagy több szolgáltatás mellett.

    ![Indítsa újra a szükséges ikon és gomb](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Válassza ki minden egyes szolgáltatás, amelynek a számítógép újraindítását, és használja a **szolgáltatás műveletek** gombra kattintva **karbantartási mód bekapcsolása**. Karbantartási mód megakadályozza, hogy a riasztások újraindítani, ha a szolgáltatás generálása.

    ![Kapcsolja be a karbantartási mód menü](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Miután engedélyezte a karbantartási mód, használja a **indítsa újra a** gombra a szolgáltatás **indítsa újra az összes érintett**

    ![Indítsa újra az összes érintett bejegyzés](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > a bejegyzéseket a **indítsa újra a** gomb más szolgáltatásokhoz eltérő lehet.

8. A szolgáltatások újraindítása, ha az a **szolgáltatás műveletek** gombra kattintva **kapcsolja ki karbantartási módba**. Az Ambari az értesítések a szolgáltatás figyelésének folytatása.

