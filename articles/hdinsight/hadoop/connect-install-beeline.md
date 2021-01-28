---
title: Az Apache Beeline-Azure HDInsight kapcsolódása vagy telepítése
description: Megtudhatja, hogyan csatlakozhat az Apache Beeline-ügyfélhez a kaptár-lekérdezések futtatásához a HDInsight Hadoop használatával. A Beeline egy olyan segédprogram, amely a HiveServer2-t a JDBC protokollon keresztül folytatja.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: ab5dedf6718dad4f16fde59d905e2e59be5c495f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944458"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Kapcsolódás az Apache beelinhez a HDInsight-on vagy helyi telepítése

Az [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) egy kaptár-ügyfél, amely a HDInsight-fürt fő csomópontjain található. Ez a cikk azt ismerteti, hogyan csatlakozhat a HDInsight-fürtön telepített Beeline-ügyfélhez különböző típusú kapcsolatokon keresztül. Azt is ismerteti, hogyan [telepítheti helyileg a Beeline-ügyfelet](#install-beeline-client). 

## <a name="types-of-connections"></a>A kapcsolatok típusai

### <a name="from-an-ssh-session"></a>SSH-munkamenetből

Ha egy SSH-munkamenetből egy fürt átjárócsomóponthoz kapcsolódik, akkor a porton a következőhöz kapcsolódhat `headnodehost` `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Azure-Virtual Network

Amikor egy ügyfélről csatlakozik egy Azure-Virtual Network HDInsight keresztül, meg kell adnia a fürt fő csomópontjának teljes tartománynevét (FQDN). Mivel ez a kapcsolódás közvetlenül a fürtcsomópontok számára történik, a csatlakozás a portot használja `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Cserélje le a helyére `<headnode-FQDN>` egy fürt átjárócsomóponthoz teljes tartománynevét. A átjárócsomóponthoz teljes tartománynevének megkereséséhez használja a [HDInsight kezelése az Apache Ambari REST API dokumentum segítségével](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) című témakört.

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Enterprise Security Package (ESP) fürt HDInsight Kerberos használatával

Amikor az ügyfélről egy Enterprise Security Package (ESP) fürthöz csatlakozik, amely a (z) Azure Active Directory (HRE)-DS-hez csatlakozik a fürt ugyanazon tartományában lévő számítógépen, meg kell adnia a tartomány nevét `<AAD-Domain>` és a fürt eléréséhez szükséges engedélyekkel rendelkező tartományi felhasználói fiók nevét is `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Cserélje le a `<username>` nevet a tartomány egy olyan fiókjának nevére, amely a fürt elérésére vonatkozó engedélyekkel rendelkezik. Cserélje le a helyére annak a `<AAD-DOMAIN>` Azure Active Directorynak (HRE) a nevét, amelyhez a fürt csatlakozik. Használjon nagybetűs karakterláncot az `<AAD-DOMAIN>` értékhez, ellenkező esetben a hitelesítő adat nem található. `/etc/krb5.conf`Szükség esetén ellenőrizze a tartománynevek nevét.

A Ambari-ből származó JDBC URL-cím megkeresése:

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` , ahol a a `CLUSTERNAME` fürt neve. Győződjön meg arról, hogy a HiveServer2 fut.

1. A HiveServer2 JDBC URL-cím másolásához használja a vágólapot.

### <a name="over-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

Ha nyilvános vagy privát végpontokat használó fürthöz csatlakozik, meg kell adnia a fürt bejelentkezési fiókjának nevét (alapértelmezett `admin` ) és jelszavát. Ha például egy ügyfélrendszer beelinét használja a címnek való kapcsolódáshoz `clustername.azurehdinsight.net` . Ez a kapcsolat a porton keresztül történik `443` , és a TLS/SSL protokollal van titkosítva.

Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje le a-t a fürthöz `admin` tartozó bejelentkezési fiókra. ESP-fürtök esetén használja a teljes UPN-t (például: user@domain.com ). Cserélje le a `password` nevet a fürt bejelentkezési fiókjának jelszavára.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

vagy privát végpont esetén:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

A privát végpontok alapszintű Load balancerre mutatnak, amely csak ugyanabban a régióban található virtuális hálózatok érhető el. További információért tekintse [meg a globális VNet-társítási és terheléselosztó-korlátozásokat](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . A következő `curl` paranccsal `-v` elháríthatja a nyilvános vagy privát végpontokkal kapcsolatos csatlakozási problémákat a beelin használata előtt.

### <a name="use-beeline-with-apache-spark"></a>A Beeline használata a Apache Spark

Apache Spark a HiveServer2 saját implementációját biztosítja, amelyet más néven a Spark-takarékossági kiszolgálónak nevezünk. A szolgáltatás a Spark SQL használatával oldja fel a lekérdezéseket a struktúra helyett. A és a lekérdezéstől függően jobb teljesítményt nyújthat.

#### <a name="through-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

A használt kapcsolatok karakterlánca némileg eltér. Ahelyett, hogy a-t `httpPath=/hive2` használja `httpPath/sparkhive2` . Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje le a-t a fürthöz `admin` tartozó bejelentkezési fiókra. ESP-fürtök esetén használja a teljes UPN-t (például: user@domain.com ). Cserélje le a `password` nevet a fürt bejelentkezési fiókjának jelszavára.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

vagy privát végpont esetén:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

A privát végpontok alapszintű Load balancerre mutatnak, amely csak ugyanabban a régióban található virtuális hálózatok érhető el. További információért tekintse [meg a globális VNet-társítási és terheléselosztó-korlátozásokat](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . A következő `curl` paranccsal `-v` elháríthatja a nyilvános vagy privát végpontokkal kapcsolatos csatlakozási problémákat a beelin használata előtt.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>A fürt feje vagy az Azure Virtual Networkon belül Apache Spark

Ha közvetlenül a fürt fő csomópontja vagy egy, a HDInsight-fürttel azonos Azure-Virtual Network található erőforráshoz csatlakozik, akkor a portot `10002` kell használni a Spark takarékosság-kiszolgálóhoz a helyett `10001` . Az alábbi példa bemutatja, hogyan csatlakozhat közvetlenül a fő csomóponthoz:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>A Beeline-ügyfél telepítése

Bár a Beeline szerepel a fő csomópontokon, érdemes helyileg telepítenie.  A helyi gépek telepítési lépései a [Linux Windows alrendszerén](/windows/wsl/install-win10)alapulnak.

1. Frissítési csomagok listája. Adja meg a következő parancsot a bash-rendszerhéjban:

    ```bash
    sudo apt-get update
    ```

1. Telepítse a Java-t, ha nincs telepítve. A paranccsal megtekintheti a `which java` parancsot.

    1. Ha nincs telepítve Java-csomag, írja be a következő parancsot:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Nyissa meg a bashrc fájlt (gyakran megtalálható a ~/.bashrc): `nano ~/.bashrc` .

    1. Módosítsa a bashrc fájlt. Adja hozzá a következő sort a fájl végéhez:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Ezután nyomja le a **CTRL + X** billentyűkombinációt, majd az **Y**, majd a ENTER billentyűt.

1. Töltse le a Hadoop és a Beeline archívumokat, és írja be a következő parancsokat:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Csomagolja ki az archívumokat, írja be a következő parancsokat:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Módosítsa a bashrc-fájlt. Meg kell határoznia azt az elérési utat, ahová az archívumok kicsomagolása megtörtént. Ha a [Linux Windows alrendszerét](/windows/wsl/install-win10)használja, és pontosan követte a lépéseket, az elérési út az `/mnt/c/Users/user/` , ahol a a `user` Felhasználónév.

    1. Nyissa meg a fájlt: `nano ~/.bashrc`

    1. Módosítsa az alábbi parancsokat a megfelelő elérési úttal, majd írja be őket a bashrc fájl végére:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Ezután nyomja le a **CTRL + X** billentyűkombinációt, majd az **Y**, majd a ENTER billentyűt.

1. Zárjuk be, majd nyissa meg újra a bash-munkamenetet.

1. A kapcsolatok tesztelése. Használja a kapcsolat formátumát a fentiekben [nyilvános vagy privát végpontokon](#over-public-or-private-endpoints).

## <a name="next-steps"></a>Következő lépések

* Példák a Beeline-ügyfél Apache Hive használatával történő használatára: az [Apache Beeline használata a Apache Hive](apache-hadoop-use-hive-beeline.md)
* További általános információk a HDInsight-beli Kaptárról: [Apache Hive használata a Apache Hadoop használatával a HDInsight](hdinsight-use-hive.md)