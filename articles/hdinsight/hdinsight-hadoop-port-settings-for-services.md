---
title: A Hadoop Services által a HDInsight-ben használt portok – Azure
description: Ez a cikk az Azure HDInsight futó Apache Hadoop-szolgáltatások által használt portok listáját tartalmazza
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b5dadb886078f701bb01447efd606c862ee73073
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945529"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>A HDInsight Apache Hadoop Services által használt portok

Ez a dokumentum a HDInsight-fürtökön futó Apache Hadoop-szolgáltatások által használt portok listáját tartalmazza. Emellett információkat nyújt a fürt SSH-kapcsolaton keresztüli csatlakozásához használt portokról is.

## <a name="public-ports-vs-non-public-ports"></a>Nyilvános portok és nem nyilvános portok

A Linux-alapú HDInsight-fürtök csak három portot tesznek elérhetővé nyilvánosan az interneten: 22, 23 és 443. Ezek a portok biztonságos hozzáférést biztosítanak a fürt számára a biztonságos HTTPS protokollon keresztül elérhető SSH és szolgáltatások használatával.

A HDInsight-t számos Azure-Virtual Machines (fürtcsomópontok) implementálja, amely Azure-Virtual Network fut. A virtuális hálózaton belülről elérheti az interneten keresztül nem elérhető portokat. Ha SSH-kapcsolaton keresztül csatlakozik a fő csomóponthoz, akkor közvetlenül hozzáférhet a fürtcsomópontokon futó szolgáltatásokhoz.

> [!IMPORTANT]  
> Ha nem ad meg Azure-Virtual Network a HDInsight konfigurációs beállításaként, az egyik automatikusan létrejön. Ehhez a virtuális hálózathoz azonban nem csatlakozhat más gépekhez (például az Azure Virtual Machines vagy az ügyféloldali fejlesztői géphez).

Ha további gépeket szeretne csatlakoztatni a virtuális hálózathoz, először létre kell hoznia a virtuális hálózatot, majd meg kell adnia a HDInsight-fürt létrehozásakor. További információ: [virtuális hálózat megtervezése a HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Nyilvános portok

Egy HDInsight-fürt összes csomópontja egy Azure-Virtual Network található. A csomópontok nem érhetők el közvetlenül az internetről. A nyilvános átjárók a következő portokhoz biztosítanak internet-hozzáférést, amelyek az összes HDInsight esetében közösek.

| Szolgáltatás | Port | Protokoll | Description |
| --- | --- | --- | --- |
| sshd |22 |SSH |Összekapcsolja az ügyfeleket az sshd-vel az elsődleges átjárócsomóponthoz. További információ: az [SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Összekapcsolja az ügyfeleket az sshd-vel a peremhálózati csomóponton. További információ: az [SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Összekapcsolja az ügyfeleket az sshd-vel a másodlagos átjárócsomóponthoz. További információ: az [SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari webes felhasználói felület. Lásd: [a HDInsight kezelése az Apache Ambari webes felületén](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Lásd: [a HDInsight kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Lásd:  [a MapReduce használata a curl használatával](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Csatlakozás a Kaptárhoz ODBC használatával. Lásd: [az Excel és a HDInsight összekötése a Microsoft ODBC-illesztővel](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Csatlakozás a ApacheHive a JDBC használatával. Lásd: [kapcsolódás Apache Hive a HDInsight a kaptár JDBC-illesztőprogram használatával](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

A következő típusok érhetők el adott fürtökhöz:

| Szolgáltatás | Port | Protokoll | Fürt típusa | Description |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |HBase REST API. Lásd: [az Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Lásd: [Apache Spark feladatok távoli elküldése az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md) |
| Spark-takarékos kiszolgáló |443 |HTTPS |Spark |A Spark-takarékossági kiszolgáló a kaptár-lekérdezések elküldésére szolgál. Lásd: [a beeline Apache Hive használata a HDInsight-on](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm webes felhasználói felület. Lásd: [Apache Storm-topológiák üzembe helyezése és kezelése a HDInsight-](storm/apache-storm-deploy-monitor-topology-linux.md) ben |
| Kafka Rest-proxy |443 |HTTPS |Kafka |Kafka-REST API. További információ: [Apache Kafka-fürtök használata az Azure HDINSIGHT Rest-proxy használatával](kafka/rest-proxy.md) |

### <a name="authentication"></a>Hitelesítés

Az interneten nyilvánosan elérhető összes szolgáltatást hitelesíteni kell:

| Port | Hitelesítő adatok |
| --- | --- |
| 22 vagy 23 |A fürt létrehozásakor megadott SSH-felhasználói hitelesítő adatok |
| 443 |A fürt létrehozása során beállított bejelentkezési név (alapértelmezett: rendszergazda) és jelszó |

## <a name="non-public-ports"></a>Nem nyilvános portok

> [!NOTE]  
> Egyes szolgáltatások csak bizonyos típusú fürtökön érhetők el. Például a HBase csak HBase-fürtökön érhető el.

> [!IMPORTANT]  
> Egyes szolgáltatások egyszerre csak egy átjárócsomóponthoz futnak. Ha az elsődleges átjárócsomóponthoz próbál csatlakozni a szolgáltatáshoz, és hibaüzenetet kap, próbálkozzon újra a másodlagos átjárócsomóponthoz használatával.

### <a name="ambari"></a>Ambari

| Szolgáltatás | Csomópontok | Port | URL-cím elérési útja | Protokoll |
| --- | --- | --- | --- | --- |
| Ambari webes felhasználói felület | Fő csomópontok | 8080 | / | HTTP |
| Ambari REST API | Fő csomópontok | 8080 | /api/v1 | HTTP |

Angol nyelvű Példák:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| NameNode webes felhasználói felület |Fő csomópontok |30070 |HTTPS |Az állapot megtekintésére szolgáló webes KEZELŐFELÜLET |
| NameNode metaadat-szolgáltatás |fő csomópontok |8020 |IPC |Fájlrendszer metaadatainak |
| DataNode |Minden munkavégző csomópont |30075 |HTTPS |Webes felhasználói felület az állapot, a naplók és így megtekinthető. |
| DataNode |Minden munkavégző csomópont |30010 |&nbsp; |Adatátvitel |
| DataNode |Minden munkavégző csomópont |30020 |IPC |Metaadat-műveletek |
| Másodlagos NameNode |Fő csomópontok |50090 |HTTP |Ellenőrzőpont a NameNode-metaadatokhoz |

### <a name="yarn-ports"></a>FONALas portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| Resource Manager webes felhasználói felület |Fő csomópontok |8088 |HTTP |A Resource Manager webes felhasználói felülete |
| Resource Manager webes felhasználói felület |Fő csomópontok |8090 |HTTPS |A Resource Manager webes felhasználói felülete |
| Resource Manager felügyeleti felület |fő csomópontok |8141 |IPC |Alkalmazás-beadványokhoz (struktúra, kaptár-kiszolgáló, Pig stb.) |
| Resource Manager-ütemező |fő csomópontok |8030 |HTTP |Felügyeleti felület |
| Resource Manager-alkalmazás felülete |fő csomópontok |8050 |HTTP |Az Applications Manager felületének címe |
| NodeManager |Minden munkavégző csomópont |30050 |&nbsp; |A Container Manager címe |
| NodeManager webes felhasználói felület |Minden munkavégző csomópont |30060 |HTTP |Resource Manager-felület |
| Idősor címe |Fő csomópontok |10200 |RPC |Az ütemterv szolgáltatás RPC szolgáltatása. |
| Ütemterv webes felhasználói felülete |Fő csomópontok |8188 |HTTP |Az idővonal-szolgáltatás webes felhasználói felülete |

### <a name="hive-ports"></a>Struktúra portjai

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| HiveServer2 |Fő csomópontok |10001 |Thrift |Szolgáltatás a Kaptárhoz való csatlakozáshoz (takarékosság/JDBC) |
| Hive-metaadattár |Fő csomópontok |9083 |Thrift |Szolgáltatás a kaptár-metaadatokhoz való csatlakozáshoz (takarékosság/JDBC) |

### <a name="webhcat-ports"></a>Webhcaten-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| Webhcaten-kiszolgáló |Fő csomópontok |30111 |HTTP |Webes API a HCatalog és más Hadoop-szolgáltatásokhoz |

### <a name="mapreduce-ports"></a>MapReduce-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| JobHistory |Fő csomópontok |19888 |HTTP |MapReduce JobHistory webes felhasználói felület |
| JobHistory |Fő csomópontok |10020 |&nbsp; |MapReduce JobHistory-kiszolgáló |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Közbenső leképezési kimenetek továbbítása a szűkítők igényléséhez |

### <a name="oozie"></a>Oozie

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| Oozie-kiszolgáló |Fő csomópontok |11000 |HTTP |Oozie-szolgáltatás URL-címe |
| Oozie-kiszolgáló |Fő csomópontok |11001 |HTTP |Oozie-rendszergazda portja |

### <a name="ambari-metrics"></a>Ambari-metrikák

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| Idősor (alkalmazás előzményei) |Fő csomópontok |6188 |HTTP |Az idővonal-szolgáltatás webes felhasználói felülete |
| Idősor (alkalmazás előzményei) |Fő csomópontok |30200 |RPC |Az idővonal-szolgáltatás webes felhasználói felülete |

### <a name="hbase-ports"></a>HBase-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| HMaster |Fő csomópontok |16000 |&nbsp; |&nbsp; |
| HMaster-információ webes felhasználói felülete |Fő csomópontok |16010 |HTTP |A HBase Master webes felhasználói felületének portja |
| Régió-kiszolgáló |Minden munkavégző csomópont |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Az ügyfelek által a ZooKeeper való csatlakozáshoz használt port |

### <a name="kafka-ports"></a>Kafka-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Description |
| --- | --- | --- | --- | --- |
| Bróker |Munkavégző csomópontok |9092 |Kafka-huzal protokoll |Ügyfél-kommunikációhoz használatos |
| &nbsp; |Zookeeper-csomópontok |2181 |&nbsp; |Az ügyfelek által a Zookeeper való csatlakozáshoz használt port |
| REST-proxy | Kafka felügyeleti csomópontok |9400 |HTTPS |[Kafka REST-specifikáció](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | URL-cím elérési útja | Description |
| --- | --- | --- | --- | --- | --- |
| Spark-kiszolgálók |Fő csomópontok |10002 |Thrift | &nbsp; | Szolgáltatás a Spark SQL-hez való csatlakozáshoz (takarékosság/JDBC) |
| Livy-kiszolgáló | Fő csomópontok | 8998 | HTTP | &nbsp; | Az utasítások, feladatok és alkalmazások futtatására szolgáló szolgáltatás |
| Jupyter Notebook | Fő csomópontok | 8001 | HTTP | &nbsp; | Jupyter Notebook webhely |

Angol nyelvű Példák:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"` . Ebben a példában a a `10.0.0.11` Livy szolgáltatást futtató átjárócsomóponthoz IP-címe.