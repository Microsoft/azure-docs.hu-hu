---
title: Apache Spark & kaptár-kaptár Warehouse-összekötő – Azure HDInsight
description: Megtudhatja, hogyan integrálhatja Apache Spark és Apache Hive a kaptár Warehouse-összekötővel az Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869888"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Apache Spark és Apache Hive integrálása a kaptár Warehouse-összekötővel az Azure HDInsight

A Apache Hive Warehouse Connector (ÜZEMELTETHETŐ WEBMAG) egy olyan függvénytár, amely megkönnyíti a Apache Spark és a Apache Hive használatát. Olyan feladatokat támogat, mint például az adatok áthelyezése a Spark DataFrames és a kaptár táblái között. Emellett a Spark-adatfolyamok a kaptár-táblákba való átirányításával. A méhkas Warehouse-összekötő a Spark és a kaptár közötti híddal működik. Emellett a Scala, a Java és a Python programozási nyelvként is támogatja a fejlesztést.

A méhkas Warehouse-összekötő lehetővé teszi, hogy kihasználhassa a kaptár és a Spark egyedi funkcióit, és hatékony, nagy adatmennyiségű alkalmazásokat építsen ki.

Apache Hive támogatja az atomi, konzisztens, elkülönített és tartós (savas) adatbázis-tranzakciókat. A struktúrával kapcsolatos további információkért lásd: [kaptár-tranzakciók](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). A kaptár az Apache Rangern és az alacsony késésű analitikai feldolgozáson (LLAP) keresztül is részletes biztonsági ellenőrzéseket nyújt Apache Sparkban.

A Apache Spark olyan strukturált streaming API-val rendelkezik, amely a Apache Hiveban nem elérhető folyamatos átviteli képességeket biztosít. A HDInsight 4,0-es verziójától kezdve a Apache Spark 2.3.1 és Apache Hive 3.1.0 külön metaadattárak rendelkeznek. A különálló metaadattárak nehézkessé teheti az együttműködési képességet. A méhkas Warehouse-összekötő megkönnyíti a Spark és a struktúra együttes használatát. A ÜZEMELTETHETŐ WEBMAG-könyvtár a LLAP démonokból származó adatokkal párhuzamosan tölti be a Spark-végrehajtókat. Ez a folyamat hatékonyabbá és alkalmazkodóképesvé teszi a Spark és a kaptár közötti szabványos JDBC-kapcsolatok esetében.

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="a méhkas Warehouse-összekötő architektúrája" border="true":::

A kaptár-tárház összekötője által támogatott néhány művelet:

* Táblázat leírása
* Táblázat létrehozása az ork-formázott adathoz
* A kaptárak adatainak kiválasztása és a DataFrame beolvasása
* DataFrame írása a struktúrába a Batch szolgáltatásban
* Struktúra-frissítési utasítás végrehajtása
* Táblázat adatainak olvasása a kaptárból, átalakítás a Sparkban, és az új struktúra-táblázatba való írása
* DataFrame vagy Spark stream készítése a kaptárba a HiveStreaming használatával

## <a name="hive-warehouse-connector-setup"></a>A méhkas Warehouse-összekötő beállítása

> [!IMPORTANT]
> - A Spark 2,4 Enterprise Security Package rendszerű fürtökre telepített HiveServer2 Interactive-példány nem támogatott a méhkas Warehouse-összekötővel való használathoz. Ehelyett külön HiveServer2 interaktív fürtöt kell konfigurálnia a HiveServer2 interaktív számítási feladatainak üzemeltetéséhez. Egyetlen Spark 2,4-fürtöt használó struktúra-tárház összekötő konfigurációja nem támogatott.
> - A méhkas Warehouse-összekötő (ÜZEMELTETHETŐ WEBMAG) könyvtára nem támogatott olyan interaktív lekérdezési fürtökkel való használatra, ahol engedélyezve van a számítási feladatok kezelése (WLM) szolgáltatás. <br>
Olyan helyzetekben, ahol csak Spark-munkaterhelések és a ÜZEMELTETHETŐ WEBMAG-függvénytár használatára van szükség, győződjön meg arról, hogy az interaktív lekérdezési fürtön nincs engedélyezve a munkaterhelés-kezelési szolgáltatás ( `hive.server2.tez.interactive.queue` a konfiguráció nincs beállítva a kaptár konfigurációjában). <br>
Olyan forgatókönyv esetén, ahol a Spark számítási feladatok (ÜZEMELTETHETŐ WEBMAG) és a LLAP natív számítási feladatok is léteznek, két különálló interaktív lekérdezési fürtöt kell létrehoznia megosztott metaadattár-adatbázissal. Egy fürt a natív LLAP számítási feladatokhoz, ahol a WLM funkció igény szerint engedélyezhető a szükséges alapon és más fürtön a ÜZEMELTETHETŐ WEBMAG esetében, ha a WLM funkciót nem lehet konfigurálni.
Fontos megjegyezni, hogy a WLM-erőforrások csomagjait is megtekintheti a fürtökről, még akkor is, ha az csak egy fürtön van engedélyezve. Ne módosítsa az erőforrás-csomagokat abban a fürtben, ahol a WLM funkció le van tiltva, mert ez hatással lehet a WLM funkcióra más fürtben.

A méhkas Warehouse-összekötőnek külön fürtökre van szüksége a Spark és az interaktív lekérdezési munkaterhelések számára. Ezeket a lépéseket követve állíthatja be ezeket a fürtöket az Azure HDInsight.

### <a name="create-clusters"></a>Fürtök létrehozása

1. Hozzon létre egy HDInsight Spark **4,0** -fürtöt egy Storage-fiókkal és egy egyéni Azure-beli virtuális hálózattal. A fürtök Azure-beli virtuális hálózatban való létrehozásával kapcsolatos információkért lásd: [HDInsight hozzáadása meglévő virtuális hálózathoz](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Hozzon létre egy HDInsight interaktív lekérdezési (LLAP) **4,0** -fürtöt ugyanazzal a Storage-fiókkal és Azure-beli virtuális hálózattal, mint a Spark-fürt.

### <a name="configure-hwc-settings"></a>ÜZEMELTETHETŐ WEBMAG-beállítások konfigurálása

#### <a name="gather-preliminary-information"></a>Előzetes információk gyűjtése

1. Egy webböngészőből navigáljon `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` oda, ahol a LLAPCLUSTERNAME az interaktív lekérdezési fürt neve.

1. Navigáljon az **összegző**  >  **HiveServer2 interaktív JDBC URL-címére** , és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Navigáljon a  >  **speciális**  >  **speciális kaptár – site**  >  **kaptár. Zookeeper. kvórum** nevű webhelyre, és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Navigáljon a **konfigurációk**  >  **speciális**  >  **általános**  >  **kaptár. metaadattár. URI** -k elemre, és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Navigáljon a **konfigurációk**  >  **speciális**, speciális  >  **kaptár-Interactive-site**  >  **kaptár. llap. Daemon. Service. hosts** elemre, és jegyezze fel az értéket. Az érték a következőhöz hasonló lehet: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>A Spark-fürt beállításainak konfigurálása

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` oda, ahol a CLUSTERNAME a Apache Spark-fürt neve.

1. Bontsa ki az **Egyéni spark2-alapértékek** elemet.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Apache Ambari Spark2-konfiguráció" border="true":::

1. Válassza a **tulajdonság hozzáadása...** lehetőséget a következő konfigurációk hozzáadásához:

    | Konfiguráció | Érték |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> A megfelelő HDFS-kompatibilis előkészítési könyvtárra van beállítva. Ha két különböző fürttel rendelkezik, az átmeneti könyvtárnak a LLAP-fürt Storage-fiókjának átmeneti könyvtárában kell lennie, hogy a HiveServer2 hozzáférjen hozzá.  Cserélje le a `STORAGE_ACCOUNT_NAME` nevet a fürt által használt Storage-fiók nevére és a `STORAGE_CONTAINER_NAME` tároló nevére. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| Az **HiveServer2 Interactive JDBC URL-címről** korábban beszerzett érték |
    |`spark.datasource.hive.warehouse.metastoreUri`| A **kaptár. metaadattár. URI**-k által korábban beszerzett érték. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` a FONALas fürt mód és `false` a fonal ügyféloldali üzemmódja esetében. |
    |`spark.hadoop.hive.zookeeper.quorum`| A **kaptár. Zookeeper. kvórum** által korábban beszerzett érték. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| A **kaptár. llap. Daemon. Service. hosts** által korábban beszerzett érték. |

1. Mentse a módosításokat, és indítsa újra az összes érintett összetevőt.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>ÜZEMELTETHETŐ WEBMAG konfigurálása Enterprise Security Package (ESP) fürtökhöz

A Enterprise Security Package (ESP) olyan nagyvállalati szintű képességeket biztosít, mint a Active Directory-alapú hitelesítés, a többfelhasználós támogatás és az Azure HDInsight-beli Apache Hadoop-fürtök szerepköralapú hozzáférés-vezérlése. Az ESP-vel kapcsolatos további információkért lásd: [Enterprise Security Package használata a HDInsight-ben](../domain-joined/apache-domain-joined-architecture.md).

Az előző szakaszban említett konfigurációkon kívül adja hozzá a következő konfigurációt a ÜZEMELTETHETŐ WEBMAG használatához az ESP-fürtökön.

1. A Spark-fürt Ambari webes felületén navigáljon a **Spark2**  >  **configs**  >  **Egyéni Spark2 – Alapértelmezések** elemre.

1. Frissítse a következő tulajdonságot.

    | Konfiguráció | Érték |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` oda, ahol a CLUSTERNAME az interaktív lekérdezési fürt neve. Kattintson a **HiveServer2 Interactive** elemre. Megtekintheti annak a fő csomópontnak a teljes tartománynevét (FQDN), amelyen a LLAP fut, ahogy azt a képernyőképen is látható. Cserélje le `<llap-headnode>` erre az értékre.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="kaptár-összekötő fő csomópontja" border="true":::

    * Az [SSH-parancs](../hdinsight-hadoop-linux-use-ssh-unix.md) használatával csatlakozzon az interaktív lekérdezési fürthöz. Keresse meg a `default_realm` paramétert a `/etc/krb5.conf` fájlban. Cserélje le `<AAD-DOMAIN>` ezt az értéket nagybetűs karakterláncként, ellenkező esetben a hitelesítő adat nem található.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="a méhkas Warehouse-összekötő HRE tartománya" border="true":::

    * Például: `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .
    
1. Mentse a módosításokat, és szükség szerint indítsa újra az összetevőket.

## <a name="hive-warehouse-connector-usage"></a>A méhkas Warehouse-összekötő használata

Néhány különböző módszer közül választhat az interaktív lekérdezési fürthöz való csatlakozáshoz és lekérdezések végrehajtásához a méhkas Warehouse-összekötő használatával. A támogatott módszerek a következő eszközöket tartalmazzák:

* [Spark-Shell/PySpark](../spark/apache-spark-shell.md)
* [Spark – elküldés](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Az alábbiakban néhány példát láthat a Spark ÜZEMELTETHETŐ WEBMAG való kapcsolódásra.

### <a name="spark-shell"></a>Spark – Shell

1. A Apache Spark-fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből hajtsa végre a következő parancsot a `hive-warehouse-connector-assembly` verzió megjegyzéséhez:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Szerkessze az alábbi kódot a `hive-warehouse-connector-assembly` fent azonosított verzióval. Ezután hajtsa végre a parancsot a Spark Shell elindításához:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. A Spark rendszerhéj elindítása után a rendszer a következő parancsokkal kezdheti meg a méhkas Warehouse Connector-példányok használatát:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark – elküldés

Miután létrehozta a Scala/Java-kódot és a függőségeket egy szerelvény jar-ba, az alábbi parancs használatával indítson el egy Spark-alkalmazást. Cserélje le `<VERSION>` `<APP_JAR_PATH>` a és a értéket a tényleges értékekre.

* FONAL ügyféloldali üzemmódja
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* FONALas fürt üzemmód
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

A Python esetében adja hozzá a következő konfigurációt is. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Lekérdezések futtatása Enterprise Security Package (ESP) fürtökön

Használja `kinit` a Spark-Shell vagy a Spark-Submit elindítása előtt. Cserélje le a USERNAME nevet egy olyan tartományi fiók nevére, amely jogosult a fürt elérésére, majd hajtsa végre a következő parancsot:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Az adatbiztonság biztosítása a Spark ESP-fürtökön

1. Hozzon létre egy táblázatot `demo` néhány mintaadatok használatával a következő parancsok beírásával:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Tekintse meg a tábla tartalmát a következő paranccsal. A szabályzat alkalmazása előtt a `demo` tábla megjeleníti a teljes oszlopot.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="bemutató táblázat a Ranger-szabályzat alkalmazása előtt" border="true":::

1. Alkalmazzon egy oszlop maszkolási szabályzatot, amely csak az oszlop utolsó négy karakterét jeleníti meg.  
    1. Nyissa meg a Ranger felügyeleti felhasználói felületét a következő címen: `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Kattintson a kaptár szolgáltatásra a fürthöz a **struktúra** területen.
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="Ranger Service Manager" border="true":::
    1. Kattintson a **maszkolás** lapra, és **adja hozzá az új házirendet**

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="kaptár-raktár összekötő Ranger-struktúra szabályzatának listája" border="true":::

    1. Adja meg a kívánt szabályzat nevét. Adatbázis kiválasztása: **alapértelmezett**, kaptár-tábla **: bemutató**, struktúra oszlop **: név**, felhasználó: **Rsadmin2**, hozzáférési típusok: **Select** és **részleges maszk: az elmúlt 4 megjelenítése** a **maszkolási beállítások kiválasztása** menüből. Kattintson a **Hozzáadás** parancsra.
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="házirend létrehozása" border="true":::
1. Megtekintheti a tábla tartalmát. A Ranger-szabályzat alkalmazása után csak az oszlop utolsó négy karakterét láthatjuk.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="bemutató táblázat a Ranger-szabályzat alkalmazása után" border="true":::

## <a name="next-steps"></a>Következő lépések

* [HWC- és Apache Spark-műveletek](./apache-hive-warehouse-connector-operations.md)
* [Az interaktív lekérdezés használata HDInsighttal](./apache-interactive-query-get-started.md)
* [HWC-integráció az Apache Zeppelinnel](./apache-hive-warehouse-connector-zeppelin.md)
* [Példák a méhkas Warehouse-összekötővel való interakcióra a Zeppelin, a Livy, a Spark-Submit és a pyspark használatával](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
