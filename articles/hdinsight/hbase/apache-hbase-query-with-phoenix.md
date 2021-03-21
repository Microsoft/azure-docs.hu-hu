---
title: 'Gyors útmutató: Apache HBase & Apache Phoenix – Azure HDInsight'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Apache Phoenixt a HDInsight-ben. Azt is megtudhatja, hogyan telepítheti és állíthatja be a az sqlline használata a számítógépen egy HBase-fürthöz való kapcsolódáshoz a HDInsight-ben.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6b9196d9ad5cf07cd210726bc1af7121c08094a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935987"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Gyors útmutató: az Apache HBase lekérdezése az Azure HDInsight Apache Phoenix

Ebből a rövid útmutatóból megtudhatja, hogyan futtathat HBase-lekérdezéseket az Azure HDInsight-ben a Apache Phoenix használatával. A Apache Phoenix az Apache HBase SQL-lekérdezési motorja. JDBC-illesztőként érhető el, és lehetővé teszi a HBase táblák SQL eszközzel végzett lekérdezését és kezelését. A [az sqlline használata](http://sqlline.sourceforge.net/) egy parancssori segédprogram az SQL végrehajtásához.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache HBase-fürt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md) HDInsight-fürt létrehozásához.  Győződjön meg arról, hogy a **HBase** -fürt típusát választja.

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>ZooKeeper-csomópont azonosítása

HBase-fürthöz való csatlakozáskor csatlakoznia kell az egyik Apache ZooKeeper csomóponthoz. Mindegyik HDInsight-fürt három ZooKeeper-csomóponttal rendelkezik. A curl használatával gyorsan azonosíthatók a ZooKeeper-csomópontok. Az alábbi curl-parancs szerkesztéséhez cserélje le a `PASSWORD` `CLUSTERNAME` megfelelő értékeket, majd írja be a parancsot a parancssorba:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

A kimenet egy része a következőhöz hasonlóan fog kinézni:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Jegyezze fel az értéket a `host_name` későbbi használatra.

## <a name="create-a-table-and-manipulate-data"></a>Tábla létrehozása és az adatkezelés

Az SSH-val csatlakozhat a HBase-fürtökhöz, majd Apache Phoenix használatával HBase-táblákat hozhat létre, adatbeszúrási és Adatlekérdezési adatgyűjtést végezhet.

1. Használja a `ssh` parancsot a HBase-fürthöz való kapcsolódáshoz. Szerkessze az alábbi parancsot úgy, hogy lecseréli a `CLUSTERNAME` fürt nevét, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Módosítsa a könyvtárat a Phoenix-ügyfélre. Írja be a következő parancsot:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Indítsa el a [az sqlline használata](http://sqlline.sourceforge.net/). Szerkessze az alábbi parancsot úgy, hogy lecseréli a `ZOOKEEPER` korábban azonosított ZooKeeper-csomópontra, majd beírja a következő parancsot:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Hozzon létre egy HBase táblát. Írja be a következő parancsot:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. A `!tables` HBase összes táblájának listázásához használja a az sqlline használata parancsot. Írja be a következő parancsot:

    ```sqlline
    !tables
    ```

6. Értékek beszúrása a táblába. Írja be a következő parancsot:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. A tábla lekérdezése. Írja be a következő parancsot:

    ```sql
    SELECT * FROM Company;
    ```

8. Rekord törlése. Írja be a következő parancsot:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. A tábla eldobása. Írja be a következő parancsot:

    ```hbase
    DROP TABLE Company;
    ```

10. A az sqlline használata `!quit` parancs használatával lépjen ki a az sqlline használata. Írja be a következő parancsot:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan futtathat HBase-lekérdezéseket az Azure HDInsight-ben a Apache Phoenix használatával. Ha többet szeretne megtudni a Apache Phoenixről, a következő cikk mélyebb vizsgálatot fog biztosítani.

> [!div class="nextstepaction"]
> [Apache Phoenix a HDInsightban](../hdinsight-phoenix-in-hdinsight.md)
