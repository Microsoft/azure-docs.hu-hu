---
title: Kas Warehouse-összekötő – Apache Zeppelin a Livy használatával – Azure HDInsight
description: Ismerje meg, hogyan integrálhatja a méhkas Warehouse-összekötőt az Apache Zeppelin használatával az Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 4859ff098bf3fdffbecc70608cb147d17d9d7f59
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941166"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Az Apache Zeppelin integrálása a kaptár Warehouse-összekötővel az Azure HDInsight

A HDInsight Spark-fürtök különböző tolmácsokkal rendelkező Apache Zeppelin-jegyzetfüzeteket tartalmaznak. Ebben a cikkben csak a Livy-tolmácsra koncentrálunk, hogy a Spark-táblákat a méhkas Warehouse-összekötő használatával férhessenek hozzá.

> [!NOTE]
> Ez a cikk az *engedélyezési* feltételekre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

## <a name="prerequisite"></a>Előfeltétel

Fejezze be a [kaptár Warehouse-összekötő telepítési](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) lépéseit.

## <a name="getting-started"></a>Első lépések

1. A Apache Spark-fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből hajtsa végre a következő parancsot a és a verziójának megjegyzéséhez `hive-warehouse-connector-assembly` `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Mentse a kimenetet későbbi használatra az Apache Zeppelin konfigurálásakor.

## <a name="configure-livy"></a>Livy konfigurálása

A következő konfigurációk szükségesek ahhoz, hogy a Zeppelin-táblákat a Livy értelmező használatával férhessenek hozzá.

### <a name="interactive-query-cluster"></a>Interaktív lekérdezési fürt

1. Egy webböngészőből navigáljon `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` oda, ahol a LLAPCLUSTERNAME az interaktív lekérdezési fürt neve.

1. Navigáljon a **speciális**  >  **Egyéni Core-site** elemre. Válassza a **tulajdonság hozzáadása...** lehetőséget a következő konfigurációk hozzáadásához:

    | Konfiguráció                 | Érték |
    | ----------------------------- |-------|
    | Hadoop. proxyuser. Livy. groups  | *     |
    | Hadoop. proxyuser. Livy. hosts   | *     |

1. Mentse a módosításokat, és indítsa újra az összes érintett összetevőt.

### <a name="spark-cluster"></a>Spark-fürt

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` oda, ahol a CLUSTERNAME a Apache Spark-fürt neve.

1. Bontsa ki az **Egyéni livy2-conf** elemet. Válassza a **tulajdonság hozzáadása...** lehetőséget a következő konfiguráció hozzáadásához:

    | Konfiguráció                 | Érték                                      |
    | ----------------------------- |------------------------------------------  |
    | Livy. file. local-dir-engedélyezési lista | /usr/HDP/current/hive_warehouse_connector/ |

1. Mentse a módosításokat, és indítsa újra az összes érintett összetevőt.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Livy-értelmező konfigurálása Zeppelin felhasználói felületen (Spark-fürt)

1. Egy böngészőben nyissa meg a (z `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` ) elemet, ahol a a `CLUSTERNAME` Apache Spark-fürt neve.

1. Navigáljon a **livy2**.

1. Adja hozzá a következő konfigurációkat:

    | Konfiguráció                 | Érték                                      |
    | ----------------------------- |:------------------------------------------:|
    | Livy. Spark. Hadoop. kaptár. llap. Daemon. Service. hosts | @llap0 |
    | Livy. Spark. Security. hitelesítő adatok. hiveserver2. enabled | true |
    | Livy. Spark. SQL. kaptár. llap | true |
    | Livy. Spark. fonál. Security. hitelesítő adatok. hiveserver2. enabled | true |
    | Livy. főfelhasználók | Livy, Zeppelin |
    | Livy. Spark. tégelyek | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Cserélje le a verziót az [első lépésekből](#getting-started)beszerzett értékekre. |
    | Livy. Spark. submit. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Cserélje le a verziót az [első lépésekből](#getting-started)beszerzett értékekre. |
    | Livy. Spark. SQL. Kas. hiveserver2. JDBC. URL | Állítsa be az interaktív lekérdezési fürt HiveServer2 interaktív JDBC URL-címére. |
    | Spark. Security. hitelesítő adatok. hiveserver2. enabled | true |

1. Csak ESP-fürtök esetén adja hozzá a következő konfigurációt:

    | Konfiguráció| Érték|
    |---|---|
    | Livy. Spark. SQL. Kas. hiveserver2. JDBC. URL. Principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` oda, ahol a CLUSTERNAME az interaktív lekérdezési fürt neve. Kattintson a **HiveServer2 Interactive** elemre. Megtekintheti annak a fő csomópontnak a teljes tartománynevét (FQDN), amelyen a LLAP fut, ahogy azt a képernyőképen is látható. Cserélje le `<llap-headnode>` erre az értékre.

        ![kaptár-összekötő fő csomópontja](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Az [SSH-parancs](../hdinsight-hadoop-linux-use-ssh-unix.md) használatával csatlakozzon az interaktív lekérdezési fürthöz. Keresse meg a `default_realm` paramétert a `/etc/krb5.conf` fájlban. Cserélje le `<AAD-DOMAIN>` ezt az értéket nagybetűs karakterláncként, ellenkező esetben a hitelesítő adat nem található.

        ![a méhkas Warehouse-összekötő HRE tartománya](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Például: `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .

1. Mentse a módosításokat, és indítsa újra a Livy-tolmácsot.

Ha a Livy-értelmező nem érhető el, módosítsa a `shiro.ini` Zeppelin-összetevőn belüli fájlt a Ambari-ben. További információ: az [Apache Zeppelin biztonságának konfigurálása](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Lekérdezések futtatása a Zeppelin-ben 

Zeppelin-jegyzetfüzet elindítása a Livy-értelmező használatával és a következők végrehajtása

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Következő lépések

* [HWC- és Apache Spark-műveletek](./apache-hive-warehouse-connector-operations.md)
* [HWC-integráció az Apache Sparkkal és az Apache Hive-val](./apache-hive-warehouse-connector.md)
* [Az interaktív lekérdezés használata HDInsighttal](./apache-interactive-query-get-started.md)
