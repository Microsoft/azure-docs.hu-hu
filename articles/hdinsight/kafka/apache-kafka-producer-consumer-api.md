---
title: 'Oktatóanyag: Apache Kafka producer & fogyasztói API-k – Azure HDInsight'
description: Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával. Ez az oktatóanyag ezen API-k használatát mutatja be egy Java-alkalmazásból futtatott Kafka on HDInsighttal.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: e5a635a8837aadaf423c6f3a0925dbac4080e60f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945175"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata

Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával.

A Kafka Producer API segítségével az alkalmazások adatstreameket küldhetnek a Kafka-fürtnek. A Kafka Consumer API segítségével az alkalmazások adatstreameket olvashatnak be a fürtből.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * A kód értelmezése
> * Az alkalmazás létrehozása és üzembe helyezése
> * Az alkalmazás futtatása a fürtön

Az API-król további információkat az Apache dokumentációjának [Producer API](https://kafka.apache.org/documentation/#producerapi)-val és [Consumer API](https://kafka.apache.org/documentation/#consumerapi)-val foglalkozó részeiben talál.

## <a name="prerequisites"></a>Előfeltételek

* Apache Kafka a HDInsight-fürtön. A fürt létrehozásával kapcsolatos további információkért tekintse [meg a Start with apache Kafka on HDInsight](apache-kafka-get-started.md)című témakört.
* A [Java Developer Kit (JDK) 8-as verziója](/azure/developer/java/fundamentals/java-jdk-long-term-support) vagy azzal egyenértékű, például OpenJDK.
* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.
* Egy SSH-ügyfél, például a Putty. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>A kód értelmezése

A példaként szolgáló alkalmazás a (z) [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) `Producer-Consumer` alkönyvtárban található. Ha **Enterprise Security Package (ESP)** engedélyezett Kafka-fürtöt használ, használja az `DomainJoined-Producer-Consumer` alkönyvtárban található alkalmazás verzióját.

Az alkalmazás elsődlegesen négy fájlból áll:
* `pom.xml`: Ez a fájl határozza meg a projektfüggőségeket, a Java-verziót és a csomagolási módszereket.
* `Producer.java`: Ez a fájl véletlenszerű mondatokat küld a Kafkának a Producer API használatával.
* `Consumer.java`: Ez a fájl a Consumer API-t használatával olvas ki adatokat a Kafkából, és az STDOUT-ba küldi el azokat.
* `AdminClientWrapper.java`: Ez a fájl a rendszergazdai API-t használja a Kafka-témakörök létrehozására, leírására és törlésére.
* `Run.java`: A Producer- és Consumer-kód futtatásához a rendszer a parancssori felületet használja.

### <a name="pomxml"></a>Pom.xml

A `pom.xml` fájl fontosabb elemei a következők:

* Függőségek: Ez a projekt a Kafka Producer és Consumer API-jaira támaszkodik, amelyeket a `kafka-clients` csomag tartalmaz. Ezt a függőséget a következő XML-kód határozza meg:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    A `${kafka.version}` bejegyzés a `pom.xml` fájl `<properties>..</properties>` szakaszában van meghatározva, és a HDInsight-fürt Kafka-verziójához van konfigurálva.

* Beépülő modulok: A Maven beépülő modulok különböző képességekkel rendelkeznek. Ebben a projektben a következő beépülő modulokat használjuk:

    * `maven-compiler-plugin`: Ezzel állítható a projekt által használt Java-verzió a 8-as verzióra. A HDInsight 3.6 ezt a Java-verziót használja.
    * `maven-shade-plugin`: Ez az alkalmazást és az esetleges függőségeket tartalmazó uber JAR-fájl létrehozására használható. Az alkalmazás belépési pontjának beállítására is használható, így közvetlenül futtathatja a JAR-fájlt anélkül, hogy a fő osztályt meg kellene határoznia.

### <a name="producerjava"></a>Producer.java

A Producer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, és adatok küld egy Kafka-témakörnek. A következő kódrészlet a [GitHub-tárházból](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) származó [producer. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) fájlból származik, és bemutatja, hogyan állíthatja be a gyártói tulajdonságokat. A vállalati biztonsági beállításokkal rendelkező fürtök esetében további tulajdonságot kell hozzáadni "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

A Consumer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, majd egy ciklusban beolvassa az adatokat. A Consumer [. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) fájl következő kódrészlete állítja be a felhasználó tulajdonságait. A vállalati biztonsági beállításokkal rendelkező fürtök esetében további tulajdonságot kell hozzáadni "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Ebben a kódban a Consumer úgy van beállítva, hogy a beolvasást a témakör elejétől kezdje (az `auto.offset.reset` beállítása: `earliest`).

### <a name="runjava"></a>Run.java

A [Run. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fájl olyan parancssori felületet biztosít, amely a gyártó vagy a fogyasztó kódját futtatja. Paraméterként a Kafka közvetítő gazdagépének adatait kell megadni. Igény szerint a felhasználói folyamat által használt csoportazonosító értéket is megadhatja. Ha ugyanazzal a csoport-AZONOSÍTÓval hoz létre több fogyasztói példányt, a rendszer a témakörből olvassa be a terheléselosztási egyenleget.

## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

### <a name="use-pre-built-jar-files"></a>Előre elkészített JAR-fájlok használata

Töltse le az adattégelyeket a [Kafka első lépések Azure-mintából](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Ha a fürt **Enterprise Security Package (ESP)** engedélyezve van, használja a Kafka-producer-Consumer-ESP. jar fájlt. Az alábbi parancs használatával másolja a tégelyeket a fürtbe.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>A JAR-fájlok létrehozása kódból

Ha ki szeretné hagyni ezt a lépést, az előre összeépített tégelyek az `Prebuilt-Jars` alkönyvtárból tölthetők le. Töltse le a Kafka-producer-Consumer. jar fájlt. Ha a fürt **Enterprise Security Package (ESP)** engedélyezve van, használja a Kafka-producer-Consumer-ESP. jar fájlt. Futtassa a 3. lépést a jar a HDInsight-fürtre történő másolásához.

1. Töltse le és csomagolja ki a példákat [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) .

2. Állítsa be az aktuális könyvtárat a `hdinsight-kafka-java-get-started\Producer-Consumer` könyvtár helyére. Ha **Enterprise Security Package (ESP)** engedélyezett Kafka-fürtöt használ, állítsa be a helyet az `DomainJoined-Producer-Consumer` alkönyvtárra. Az alkalmazás létrehozásához használja a következő parancsot:

    ```cmd
    mvn clean package
    ```

    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-producer-consumer-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza. Az ESP-fürtök esetén a fájl a következő lesz: `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. A következő parancs beírásával másolja a `kafka-producer-consumer-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtre. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> A példa futtatása

1. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Nyisson meg egy SSH-kapcsolatokat a fürttel a következő parancs beírásával. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A Kafka-átvitelszervező gazdagépek beszerzéséhez cserélje le az értékeket a `<clustername>` `<password>` következő parancsban, majd hajtsa végre. Használja ugyanazt a burkolatot, `<clustername>` mint ahogy az a Azure Portalban látható. Cserélje le `<password>` a nevet a fürt bejelentkezési jelszavára, majd hajtsa végre a következőket:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ehhez a parancshoz Ambari-hozzáférés szükséges. Ha a fürt egy NSG mögött található, futtassa ezt a parancsot egy olyan gépről, amely hozzáférhet a Ambari.

1. A Kafka-témakör létrehozásához `myTest` írja be a következő parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Az előállító futtatásához és az adatok témakörbe írásához használja az alábbi parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Az előállító futásának befejeződését követően használja az alábbi parancsot a témakörből történő olvasáshoz:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt.

1. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

### <a name="multiple-consumers"></a>Több fogyasztó

A Kafka-fogyasztók egy fogyasztói csoportot használnak a rekordok olvasásakor. Ugyanazon csoport használata több fogyasztó esetén a terhelés szempontjából kiegyensúlyozott olvasást eredményez a témakörökből történő olvasáskor. A csoport mindegyik fogyasztója a rekordok egy részét kapja meg.

A fogyasztó alkalmazás elfogadja a csoportazonosítóként használt paramétert. Az alábbi parancs például elindít egy fogyasztót a `myGroup` csoportazonosítóval:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

Ha szeretné látni, hogyan zajlik a folyamat, használja az alábbi parancsot:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Ez a parancs a `tmux` használatával két oszlopra osztja a terminált. Mindegyik oszlopban elindul egy-egy fogyasztó, amelyekhez azonos csoportazonosító-érték tartozik. Ha a fogyasztók befejezték a beolvasást, figyelje meg, hogy mindegyik csak a rekordok egy részét olvasta be. A __CTRL + C billentyűkombinációt__ a kilépéshez használhatja `tmux` .

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. Ebben a kódmintában a korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha elindít nyolc fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]  
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb nyolc fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb nyolc fogyasztóval rendelkező fogyasztói csoportja is.

A Kafka-ben tárolt rekordok tárolása a partíción belül kapott sorrendben történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="common-issues-faced"></a>Gyakori problémák

1. A **témakör létrehozása sikertelen** Ha a fürtön engedélyezve van a vállalati biztonsági csomag, használja az [előre elkészített jar-fájlokat a gyártó és a fogyasztó számára](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). Az ESP jar a programkódból is létrehozható az [ `DomainJoined-Producer-Consumer` alkönyvtárban](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). A gyártó és a fogyasztó tulajdonságai további tulajdonságot `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` biztosítanak az ESP-kompatibilis fürtökhöz.

2. **Nem sikerült az ESP-kompatibilis fürtökön**: Ha a termelés és a felhasználás művelet meghiúsul, és ESP-t használó fürtöt használ, ellenőrizze, hogy a felhasználó szerepel `kafka` -e az összes Ranger-házirendben. Ha nincs jelen, adja hozzá az összes Ranger-szabályzathoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan használhatja a Apache Kafka producer és fogyasztói API-t a Kafka on HDInsight. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [A Kafka REST proxy használata](rest-proxy.md)
* [Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)