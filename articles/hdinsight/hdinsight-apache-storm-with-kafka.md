---
title: 'Oktatóanyag: Az Apache kafka platformmal – Azure HDInsight adatok olvasását és írását az Apache Storm használatával'
description: Megtudhatja, hogyan hozhat létre streamelési folyamatot az Apache Storm és az Apache Kafka on HDInsight használatával. Ebben az oktatóanyagban a KafkaBolt és a KafkaSpout összetevőkkel streamel adatokat a Kafkából.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/06/2018
ms.openlocfilehash: bf62b4f8f8dbfc7df73102bb06e4f16c0fdb806c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097317"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Oktatóanyag: Az Apache Kafka on HDInsight az Apache Storm használata

Ez az oktatóanyag bemutatja, hogyan használható egy [Apache Storm](https://storm.apache.org/) olvasása és írása az adatokat a topológia [Apache Kafka](https://kafka.apache.org/) a HDInsight. Ebben az oktatóanyagban is bemutatja, hogyan megőrizni az adatokat a [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) kompatibilis tárolási, a Storm-fürtön.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Storm és Kafka
> * A kód értelmezése
> * Kafka- és Storm-fürtök létrehozása
> * A topológia létrehozása
> * A topológia konfigurálása
> * A Kafka-témakör létrehozása
> * A topológiák elindítása
> * A topológiák leállítása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* A Kafka-témakörök létrehozásának ismerete. További információkért lásd [a Kafka on HDInsight használatába bevezető](./kafka/apache-kafka-get-started.md) dokumentumot.

* A Storm-megoldások (topológiák) létrehozásának és üzembe helyezésének ismerete. Pontosabban a használó topológiák [Apache Storm fluxus](https://storm.apache.org/releases/current/flux.html) keretrendszer. További információkért lásd: a [egy Apache Storm-topológia létrehozása Java nyelven](./storm/apache-storm-develop-java-topology.md) dokumentumot.

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb verzió. A HDInsight 3.5 vagy magasabb verziójához Java 8 szükséges.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH-ügyfél (az `ssh` és az `scp` parancsokra lesz szükség). – További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Az alábbi környezeti változók állíthatók be a Java és a JDK fejlesztői munkaállomáson történő telepítésekor. Érdemes azonban ellenőriznie, hogy a fentiek léteznek-e, és a rendszer számára megfelelő értékeket tartalmaznak-e.

* `JAVA_HOME` – arra a könyvtárra mutasson, ahová a JDK telepítve lett.
* `PATH` – a következő elérési utakat kell tartalmaznia:
  
    * `JAVA_HOME` (vagy ezzel egyenértékű elérési út).
    * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési út).
    * A Maven telepítési könyvtára.

> [!IMPORTANT]  
> A dokumentum lépéseihez egy olyan Azure-erőforráscsoport szükséges, amely Storm on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Storm-fürt közvetlenül kommunikálhat a Kafka-fürttel.
> 
> A kényelmes használat érdekében ez a dokumentum tartalmaz egy hivatkozást egy olyan sablonra, amellyel az összes szükséges Azure-erőforrás létrehozható. 
>
> A HDInsight a virtuális hálózatokon való használatával kapcsolatos további információért tekintse meg [a HDInsight virtuális hálózattal való kiterjesztését ismertető](hdinsight-extend-hadoop-virtual-network.md) dokumentumot.

## <a name="storm-and-kafka"></a>Storm és Kafka

Az Apache Storm az Apache kafka platformmal használata a több összetevőt tartalmaz. Ebben az oktatóanyagban a következő összetevőket használjuk:

* `org.apache.storm.kafka.KafkaSpout`: Ez az összetevő Kafka adatokat olvas be. Ez az összetevő a következő összetevőkre épül:

    * `org.apache.storm.kafka.SpoutConfig`: A spout-összetevő konfigurációs biztosít.

    * `org.apache.storm.spout.SchemeAsMultiScheme` és `org.apache.storm.kafka.StringScheme`: Az adatok a Kafkából hogyan Storm rekordot átalakításának.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Ez az összetevő írja az adatokat a Kafka. Ez az összetevő a következő összetevőkre épül:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: A témakör írt ismerteti.

    * `org.apache.kafka.common.serialization.StringSerializer`: Konfigurálja a bolt szerializálni az adatokat egy karakterláncértéket.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: A rekord adatstruktúra, a Storm-topológia a Kafkában tárolt mezők belül használt térképeit.

Ezek az összetevők az `org.apache.storm : storm-kafka` csomagban érhetőek el. Használja a Storm-verziónak megfelelő csomagverziót. A HDInsight 3.6 esetében a Storm-verzió az 1.1.0.
Az `org.apache.kafka : kafka_2.10` csomagra is szükség lesz, amely további Kafka összetevőket tartalmaz. Használja a Kafka-verziónak megfelelő csomagverziót. A HDInsight 3.6 esetében a Kafka-verzió a 0.10.0.0.

A következő XML-kódja a függőségi deklaráció a `pom.xml` számára egy [Apache Maven](https://maven.apache.org/) projekt:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>0.10.0.0</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>A kód értelmezése

A dokumentumban használt kód a következő helyen található: [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Az oktatóanyagban két topológia szerepel:

* Kafka-writer: Állít elő mondatokat véletlenszerű, és tárolja a Kafka.

* Kafka-reader: Adatokat olvas be a Kafka, és tárolják a Storm-fürt HDFS-kompatibilis fájl áruházzal.

    > [!WARNING]  
    > Ahhoz, hogy a Storm működjön a HDInsight által használt HDFS-kompatibilis tárolóval, egy szkriptműveletre van szükség. A szkript több JAR-fájlt tölt fel a Storm `extlib` útvonalára. Az oktatóanyagban lévő sablon automatikusan használja a szkriptet a fürtök létrehozása során.
    >
    > Ha a Storm-fürt létrehozásához nem használja a dokumentumban lévő sablont, manuálisan kell alkalmaznia a szkriptműveletet a fürtre.
    >
    > A parancsprogram-művelet a következő helyen található [ https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh ](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) és a felügyelő és nimbus csomóponttal, a Storm-fürt alkalmazza. A szkriptműveletek használatával kapcsolatos további információkért tekintse meg [a HDInsight szkriptműveletkel való testreszabását](hdinsight-hadoop-customize-cluster-linux.md) ismertető dokumentumot.

A topológiák a [Flux](https://storm.apache.org/releases/1.1.2/flux.html) használatával vannak definiálva. A Flux a Storm 0.10.x verziójával lett bevezetve, és a használatával elválaszthatja a topológia konfigurációját a kódtól. A Flux-keretrendszert használó topológiák esetében a topológia egy YAML-fájlban van definiálva. Az YAML-fájl a topológia részeként is megadható. Önálló fájlként is használható a topológia elküldésekor. A Flux támogatja a változók futásidőben való behelyettesítését is, amit ebben a példában is alkalmazunk.

A topológiákhoz az alábbi paramétereket vannak megadva futásidőben:

* `${kafka.topic}`: A Kafka-témakört, amely a topológiák olvasására vagy írására irányuló neve.

* `${kafka.broker.hosts}`: A gazdagépeket, amelyek a Kafka-közvetítőkhöz futtassa. A közvetítő adatait a KafkaBolt használja a Kafkába való íráskor.

* `${kafka.zookeeper.hosts}`: A gazdagépek, a Kafka-fürt futtató Zookeeper.

* `${hdfs.url}`: A fájl rendszer URL-címe a HDFSBolt összetevő. Azt jelzi, hogy az adatok egy Azure Storage-fiók vagy az Azure Data Lake Storage írása.

* `${hdfs.write.dir}`: A könyvtár írt adatok.

A Flux-topológiákkal kapcsolatos további információkért lásd: [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

### <a name="kafka-writer"></a>Kafka-író

A Kafka-író topológiában a Kafka bolt összetevő két sztringértéket vesz fel paraméterként. Ezek a paraméterek jelzik, hogy a rekordok melyik mezőit küldi a bolt a Kafkába __kulcs__ és __üzenet__ értékként. A kulcs az adatok particionálására használatos a Kafkában. Az üzenet maga az eltárolt adat.

Példánkban a `com.microsoft.example.SentenceSpout` összetevő egy két mezőt, a `key` és a `message` mezőket tartalmazó rekordot bocsát ki. A Kafka bolt kibontja ezeket a mezőket, és elküldi a bennük foglalt adatokat a Kafkába.

A mezőknek nem kell a `key` és a `message` nevet használniuk. Azért használjuk ezeket a neveket ebben a projektben, hogy a leképezés könnyebben megérthető legyen.

A következő YAML a Kafka-író összetevő definíciója:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-olvasó

A Kafka-olvasó topológiában a spout összetevő sztringértékekként olvassa be a Kafkából az adatokat. Az adatokat ezután a naplózási összetevő a Storm-naplóba, a HDFS-bolt összetevő pedig a Storm-fürt HDFS-kompatibilis fájlrendszerébe írja.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Tulajdonságbehelyettesítések

A projekt tartalmaz egy `dev.properties` nevű fájlt, amely a topológiák által használt paraméterek átadására használatos. A következő tulajdonságokat határozza meg:

| dev.properties fájl | Leírás |
| --- | --- |
| `kafka.zookeeper.hosts` | A [Apache ZooKeeper](https://zookeeper.apache.org/) gazdagépek, a Kafka-fürt számára. |
| `kafka.broker.hosts` | A Kafka-közvetítő gazdagépei (feldolgozó csomópontok). |
| `kafka.topic` | A topológiák által használt Kafka-témakör. |
| `hdfs.write.dir` | A könyvtár, amelybe a Kafka-olvasó topológia ír. |
| `hdfs.url` | A Storm-fürt által használt fájlrendszer. Azure Storage-fiókok esetében használja a `wasb:///` értéket. Az Azure Data Lake Storage Gen2, az érték `abfs:///`. Az Azure Data Lake Storage Gen1, az érték `adl:///`. |

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. A Kafkát használó minden eszköznek ugyanabban az Azure virtuális hálózatban kell lennie. Ebben az oktatóanyagban a Kafka- és a Storm-fürtök is ugyanabban az Azure virtuális hálózatban szerepelnek. 

Az alábbi ábra a Storm és a Kafka közötti kommunikáció áramlását mutatja be:

![Azure virtuális hálózatban lévő Storm- és Kafka-fürtök ábrája](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]  
> A fürt SSH például más szolgáltatások és [Apache Ambari](https://ambari.apache.org/) az interneten keresztül érhető el. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Azure-beli virtuális hálózat, majd az abban lévő Kafka- és Storm-fürtök létrehozásához hajtsa végre a következő lépéseket:

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Az Azure Resource Manager-sablon a következő helyen található: **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. A következő erőforrásokat hozza létre:
    
    * Azure-erőforráscsoport
    * Azure Virtual Network
    * Azure Storage-fiók
    * Kafka on HDInsight 3.6-os verzió (három feldolgozó csomópont)
    * Storm on HDInsight 3.6-os verzió (három feldolgozó csomópont)

   > [!WARNING]  
   > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. A sablon egy három feldolgozó csomópontot tartalmazó Kafka-fürtöt hoz létre.

2. A következő útmutatások mentén töltheti ki az **Egyéni üzembe helyezés** szakasz bejegyzéseit:

   1. A következő információkkal töltheti ki a **Testreszabott sablon** szakaszban lévő bejegyzéseket:

      | Beállítás | Érték |
      | --- | --- |
      | Előfizetés | Az Azure-előfizetése |
      | Erőforráscsoport | Az erőforrásokat tartalmazó erőforráscsoport. |
      | Location egység | Az az Azure-régió, amelyben az erőforrások létrejönnek. |
      | Kafka-fürt neve | A Kafka-fürt neve. |
      | Storm-fürt neve | A Storm-fürt neve. |
      | Fürt bejelentkezési felhasználóneve | A fürtök rendszergazdai felhasználóneve. |
      | Fürt bejelentkezési jelszava | A fürtök rendszergazdai felhasználójának jelszava. |
      | SSH-felhasználónév | A fürtökhöz létrehozandó SSH-felhasználó. |
      | SSH-jelszó | Az SSH-felhasználó jelszava. |
   
      ![A sablon paramétereinek képe](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget.

> [!NOTE]  
> A fürtök létrehozása 20 percig is eltarthat.

## <a name="build-the-topology"></a>A topológia létrehozása

1. A fejlesztési környezetben töltse le a projektet a [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) helyről, nyisson meg egy parancssort, és módosítsa a könyvtárakat arra a helyre, ahová a projektet letöltötte.

2. A **hdinsight-storm-java-kafka** könyvtárban fordítsa le a projektet a következő parancs használatával, és hozzon létre egy telepítési csomagot:

   ```bash
   mvn clean package
   ```

    A csomagolási folyamat egy `KafkaTopology-1.0-SNAPSHOT.jar` nevű fájlt hoz létre a `target` könyvtárban.

3. Az alábbi parancsokkal másolja a csomagot a Storm on HDInsight-fürtre: Cserélje le az `sshuser` elemet a fürt SSH-felhasználónevére. Cserélje le a `stormclustername` elemet a __Storm__-fürt nevére.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    Ha a rendszer erre kéri, adja meg a fürtök létrehozásakor használt jelszót.

## <a name="configure-the-topology"></a>A topológia konfigurálása

1. Kövesse az alábbi módszerek egyikét a **Kafka** on HDInsight-fürt Kafka-közvetítő gazdagépeinek felderítésére:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > A következő Bash-beli példa feltételezi, hogy a `$CLUSTERNAME` tartalmazza a __Kafka__-fürt nevét. Azt is feltételezi, hogy a [jq](https://stedolan.github.io/jq/) 1.5-ös vagy újabb verziója telepítve van. Ha a rendszer kéri, adja meg a fürt bejelentkezési fiókjának jelszavát.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    A visszaadott érték az alábbi szöveghez hasonló:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]  
    > Bár kettőnél több közvetítő gazdagép is létezhet a fürtön, nem kell megadnia a gazdagépek teljes listáját az ügyfeleknek. Egy vagy kettő is elég.

2. Kövesse az alábbi módszerek egyikét a __Kafka__ on HDInsight-fürt Zookeeper-gazdagépeinek felderítésére:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > A következő Bash-beli példa feltételezi, hogy a `$CLUSTERNAME` tartalmazza a __Kafka__-fürt nevét. Azt is feltételezi, hogy a [jq](https://stedolan.github.io/jq/) telepítve van. Ha a rendszer kéri, adja meg a fürt bejelentkezési fiókjának jelszavát.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    A visszaadott érték az alábbi szöveghez hasonló:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]  
    > Bár kettőnél több Zookeeper-csomópont is létezhet, nem kell megadnia a gazdagépek teljes listáját az ügyfeleknek. Egy vagy kettő is elég.

    Mentse ezt az értéket, mivel később még használni fogjuk.

3. Szerkessze a projekt gyökerében található `dev.properties` fájlt. Adja hozzá a __Kafka__-fürt közvetítő és Zookeeper-gazdagépeinek adatait a fájl megfelelő soraihoz. A következő példa a korábbi lépésekből származó mintaértékekkel van konfigurálva:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]  
    > A `hdfs.url` bejegyzés egy Azure Storage-fiókot használó fürthöz van konfigurálva. Ez a topológia a Storm-fürt által használt Data Lake Storage használni, módosítsa ezt az értéket `wasb` való `adl`.

4. Mentse a `dev.properties` fájlt, majd a következő parancs használatával töltse fel a **Storm**-fürtbe:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Cserélje le a **USERNAME** elemet a fürt SSH-felhasználónevére. Cserélje le a **BASENAME** elemet a fürt létrehozásakor használt alapnévre.

## <a name="create-the-kafka-topic"></a>A Kafka-témakör létrehozása

A Kafka az adatokat _témakörökben_ tárolja. A témakört még a Storm-fürtök elindítása előtt létre kell hoznia. A topológia létrehozásához kövesse a következő lépéseket:

1. Csatlakozzon SSH-kapcsolaton keresztül a __Kafka__-fürthöz a következő parancs használatával. Cserélje le az `sshuser` elemet a fürt létrehozásakor használt SSH-felhasználónévre. Cserélje le a `kafkaclustername` elemet a Kafka-fürt nevére:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Ha a rendszer erre kéri, adja meg a fürtök létrehozásakor használt jelszót.
   
    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A Kafka-témakör létrehozásához használja az alábbi parancsot. Cserélje le a `$KAFKAZKHOSTS` elemet a topológia konfigurálásakor használt Zookeeper-gazdagép adataira:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs csatlakozik a Kafka-fürt Zookeeper-gazdagépéhez, és létrehoz egy új témakört `stormtopic` néven. Ezt a témakört a Storm-topológiák használják.

## <a name="start-the-writer"></a>Az író elindítása

1. Az alábbi paranccsal csatlakoztassa a **Storm**-fürtöt SSH-kapcsolaton keresztül. Cserélje le az `sshuser` elemet a fürt létrehozásakor használt SSH-felhasználónévre. Cserélje le a `stormclustername` elemet a Storm-fürt nevére:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Ha a rendszer erre kéri, adja meg a fürtök létrehozásakor használt jelszót.
   
    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Használja a következő parancsot a Storm-fürttel létesített SSH-kapcsolaton keresztül az író topológia elindításához:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    A parancs paraméterei a következők:

    * `org.apache.storm.flux.Flux`: Fluxus használatával konfigurálhatja, és futtassa ezt a topológiát.

    * `--remote`: Küldje el a Nimbus topológiát. A topológia a fürt feldolgozó csomópontjai között van elosztva.

    * `-R /writer.yaml`: Használja a `writer.yaml` fájlt a topológiát. Az `-R` azt jelzi, hogy ez az erőforrás a JAR-fájl részét képezi. Ez a JAR gyökerében található, így a `/writer.yaml` az elérési útja.

    * `--filter`: Töltse ki a bejegyzést a `writer.yaml` topológiájának szereplő értékek a `dev.properties` fájlt. Például a fájl `kafka.topic` bejegyzésének értéke lesz behelyettesítve a topológiadefiníció `${kafka.topic}` bejegyzésének helyére.

## <a name="start-the-reader"></a>Az olvasó elindítása

1. Használja a következő parancsot a Storm-fürttel létesített SSH-munkameneten keresztül az olvasó topológia elindításához:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Várjon egy percet, majd a következő paranccsal tekintse meg az olvasó topológia által létrehozott fájlokat:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    A kimenet az alábbi szöveghez hasonló:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. A fájl tartalmának megtekintéséhez használja az alábbi parancsot. Cserélje le a `filename.txt` elemet egy fájl nevére:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    A következő szöveg egy példa a fájl tartalmára:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>A topológiák leállítása

Használja a következő parancsokat a Storm-fürttel létesített SSH-munkameneten keresztül a Storm-topológiák elindításához:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
> 
> A Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan használható egy [Apache Storm](https://storm.apache.org/) olvasni és írni topológia [Apache Kafka](https://kafka.apache.org/) a HDInsight. Azt is megtanulta, hogyan tárolhatja az adatokat a [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) kompatibilis, a HDInsight által használt tárterület.

A HDInsight Kafka használatával kapcsolatos további tudnivalókért lásd: a [a Apache Kafka Producer és Consumer API-k](kafka/apache-kafka-producer-consumer-api.md) dokumentumot.

A topológiák Linux-alapú HDInsighton való telepítésével és monitorozásával kapcsolatos információkért tekintse meg [a Linux-alapú HDInsight alatt futó Apache Storm-topológiák üzembe helyezését és felügyeletét](storm/apache-storm-deploy-monitor-topology-linux.md) ismertető dokumentumot.
