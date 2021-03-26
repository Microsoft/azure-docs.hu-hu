---
title: Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz – Azure HDInsight
description: Megtudhatja, hogyan érheti el a fonal-alkalmazási naplókat egy Linux-alapú HDInsight-(Apache Hadoop-) fürtön a parancssori felület és a webböngésző használatával.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 9558e6c5ddd58b1d5fd70da03187caef50d1275d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865570"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz a Linux-alapú HDInsight

Megtudhatja, hogyan érheti el a naplókat [Apache HADOOP fonalak](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (még egy erőforrás-egyeztető) alkalmazásai számára egy Apache Hadoop-fürtön az Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Mi az Apache-fonal?

A FONALak több programozási modellt támogatnak (Apache Hadoop MapReduce is) az erőforrás-kezelés leválasztásával az alkalmazás ütemezése/monitorozása során. A fonal globális *`ResourceManager`* (RM), munkavégző-csomópontos *Csomópontkezelők* (ApplicationMasters) és alkalmazáson belüli  (AMs) használ. Az per-Application AM egyezteti az erőforrásokat (processzor, memória, lemez, hálózat) az alkalmazásnak az RM-vel való futtatásához. Az RM úgy működik, hogy az ilyen erőforrásokat *tárolóként* adja meg. Az AM feladata az RM által hozzárendelt tárolók állapotának nyomon követése. Egy alkalmazás az alkalmazás természetétől függően számos tárolót igényelhet.

Minden alkalmazás több *alkalmazási kísérletből* állhat. Ha egy alkalmazás meghibásodik, előfordulhat, hogy a rendszer újrapróbálkozik új kísérletként. Minden kísérlet egy tárolóban fut. Bizonyos értelemben a tárolók a FONALas alkalmazások által végzett Munkaegységek kontextusát biztosítják. A tároló környezetében elvégzett összes munka az egyetlen feldolgozó csomóponton történik, amelyen a tárolót adták. További információért lásd a [Hadoop: fonal-alkalmazások írása](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)vagy a [Apache Hadoop fonalak](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) .

Ha a fürtöt a nagyobb feldolgozási sebesség támogatásához kívánja méretezni, a [fürtöket manuálisan, néhány különböző nyelven](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters) [is használhatja.](hdinsight-autoscale-clusters.md)

## <a name="yarn-timeline-server"></a>FONAL idővonal-kiszolgálója

A [Apache HADOOP fonál idővonal-kiszolgálója](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) általános információkat biztosít a befejezett alkalmazásokról

A fonal idővonal-kiszolgálója a következő típusú adattípusokat tartalmazza:

* Az alkalmazás azonosítója, az alkalmazás egyedi azonosítója
* Az alkalmazást elindító felhasználó
* Információk az alkalmazás befejezésére tett kísérletekről
* Az adott alkalmazás által megkísérelt tárolók

## <a name="yarn-applications-and-logs"></a>FONALas alkalmazások és naplók

Az alkalmazás naplói (és a kapcsolódó tárolók naplói) kritikus fontosságúak a problematikus Hadoop-alkalmazások hibakereséséhez. A fonal egy szép keretrendszert biztosít az alkalmazások naplófájljainak naplózási összesítéssel való összegyűjtéséhez, összesítéséhez és tárolásához.

A naplózási összesítés funkció lehetővé teszi, hogy az alkalmazások naplói több determinisztikus. Összesíti a munkavégző csomópont összes tárolójában lévő naplókat, és a munkavégző csomóponton egy összesített naplófájlként tárolja őket. Az alkalmazás befejeződése után a rendszer az alapértelmezett fájlrendszerben tárolja a naplót. Az alkalmazás több száz vagy akár több ezer tárolót is használhat, de egyetlen feldolgozó csomóponton futtatott összes tároló naplóit mindig egyetlen fájlba összesíti a rendszer. Így az alkalmazás csak 1 log/feldolgozó csomópontot használ. A naplózási összesítés alapértelmezés szerint engedélyezve van a 3,0-es és újabb verziójú HDInsight-fürtökön. Az összesített naplók a fürt alapértelmezett tárolójában találhatók. A következő elérési út a naplók HDFS elérési útja:

```
/app-logs/<user>/logs/<applicationId>
```

Az elérési útban az `user` alkalmazást elindító felhasználó neve. Az egy `applicationId` alkalmazáshoz hozzárendelt egyedi azonosító a fonal RM használatával.

Az összesített naplók nem olvashatók közvetlenül, ahogy egy TFile, a tároló által indexelt bináris formátumban vannak írva. A FONALak `ResourceManager` naplói vagy parancssori felületi eszközeivel egyszerű szövegként tekintheti meg ezeket a naplókat alkalmazások vagy tárolók esetében.

## <a name="yarn-logs-in-an-esp-cluster"></a>Egy ESP-fürtön lévő fonal-naplók

Az egyéni Ambari-ben két konfigurációt kell hozzáadni `mapred-site` .

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net` , ahol a a `CLUSTERNAME` fürt neve.

1. A Ambari felhasználói felületén navigáljon a **MapReduce2**  >  **configs**  >  **speciális**  >  **Egyéni mapred-webhelyre**.

1. Adja hozzá a következő tulajdonságok *egyikét* :

    **1. beállítás**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **2. beállítás**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Mentse a módosításokat, és indítsa újra az összes érintett szolgáltatást.

## <a name="yarn-cli-tools"></a>FONAL CLI-eszközök

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](./hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Sorolja fel a jelenleg futó fonal-alkalmazások összes alkalmazás-azonosítóját a következő paranccsal:

    ```bash
    yarn top
    ```

    Jegyezze fel az alkalmazás AZONOSÍTÓját abban az `APPLICATIONID` oszlopban, amelynek a naplóit le szeretné tölteni.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Ezeket a naplókat egyszerű szövegként tekintheti meg a következő parancsok egyikének futtatásával:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    A &lt; parancsok futtatásakor határozza meg a applicationId>, a &lt; felhasználó által elindított-The-The-Application>, a &lt; containerId> és a &lt; feldolgozói csomópont-címek> információkat.

### <a name="other-sample-commands"></a>Egyéb minta parancsok

1. Töltse le a fonalas tárolók naplóit az összes alkalmazás-főkiszolgálóhoz az alábbi paranccsal. Ez a lépés létrehoz egy szöveges formátumban elnevezett naplófájlt `amlogs.txt` .

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Töltse le a fonal-tároló naplóit csak a legújabb alkalmazás-főkiszolgáló számára a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Töltse le a fonal-tároló naplóit az első két alkalmazás főkiszolgálói számára a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Töltse le az összes fonalas tároló naplóit a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Töltsön le egy adott tárolóhoz tartozó fonal-tároló naplót a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>FONAL `ResourceManager` felhasználói felülete

A fonal `ResourceManager` felhasználói felülete a fürt átjárócsomóponthoz fut. Ez a Ambari webes felhasználói felületén keresztül érhető el. A következő lépések végrehajtásával tekintheti meg a FONALak naplóit:

1. A böngészőben nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net` . Cserélje le a CLUSTERNAME kifejezést a HDInsight-fürt nevére.

2. A bal oldali szolgáltatások listájából válassza a **fonal** lehetőséget.

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png" alt-text="Apache Ambari fonal-szolgáltatás kiválasztva":::

3. A **rövid hivatkozások** legördülő listából válassza ki az egyik fürtcsomópont-csomópontot, majd válassza a lehetőséget **`ResourceManager Log`** .

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png" alt-text="Apache Ambari-fonal – gyors hivatkozások":::

    Megjelenik a FONALak naplóira mutató hivatkozások listája.

## <a name="next-steps"></a>Következő lépések

* [Apache Hadoop-architektúra a HDInsightban](hdinsight-hadoop-architecture.md)
* [Az Apache Hadoop YARN hibaelhárítása az Azure HDInsighttal](hdinsight-troubleshoot-yarn.md)
