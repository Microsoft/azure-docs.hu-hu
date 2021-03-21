---
title: 'Oktatóanyag: Az Apache Kafka Streams API használata – Azure HDInsight '
description: Oktatóanyag – megtudhatja, hogyan használhatja a Apache Kafka Streams API-t a Kafka on HDInsight. Az API segítségével streamfeldolgozást végezhet a témakörök között a Kafkában.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 5a1548cdf1d05a1f9d42f5c64b7fdc18f514518e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939199"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Oktatóanyag: Apache Kafka Streams API használata az Azure HDInsight

Megtudhatja, hogyan hozhat létre olyan alkalmazást, amely a Apache Kafka Streams API-t használja, és futtatja a Kafka on HDInsight.

A jelen oktatóanyagban használt alkalmazás egy streamelési szószámláló. Szöveges adatokat olvas be egy Kafka-témakörből, kigyűjti az egyes szavakat, majd eltárolja a szavakat és azok számát egy másik Kafka-témakörben.

A Kafka stream feldolgozása gyakran Apache Spark vagy Apache Storm használatával történik. A Kafka-verzió 1.1.0 (HDInsight 3,5 és 3,6) bemutatta a Kafka Streams API-t. Ez az API lehetővé teszi az adatstreamek a bemeneti és kimeneti témakörök közötti átalakítását. Bizonyos esetekben ez alternatív megoldás lehet a streamelési Storm- vagy Spark-megoldások létrehozása helyett.

A Kafka Streams megoldással kapcsolatos további információkért tekintse meg [a Streams bevezető](https://kafka.apache.org/10/documentation/streams/) dokumentációját az Apache.org webhelyen.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A kód értelmezése
> * Az alkalmazás létrehozása és üzembe helyezése
> * Kafka-témakörök konfigurálása
> * A kód futtatása

## <a name="prerequisites"></a>Előfeltételek

* Egy Kafka on HDInsight 3.6-fürt. Ha szeretné megtudni, hogyan hozhat létre Kafka-t a HDInsight-fürtön, tekintse meg a [Start with apache Kafka for HDInsight](apache-kafka-get-started.md) Document című témakört.

* Hajtsa végre a [Apache Kafka fogyasztói és termelői API-](apache-kafka-producer-consumer-api.md) dokumentum lépéseit. A dokumentumban leírt lépések az ebben az oktatóanyagban létrehozott példaalkalmazást és -témaköröket használják.

* A [Java Developer Kit (JDK) 8-as verziója](/azure/developer/java/fundamentals/java-jdk-long-term-support) vagy azzal egyenértékű, például OpenJDK.

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>A kód értelmezése

A példaként szolgáló alkalmazás a (z) [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) `Streaming` alkönyvtárban található. Az alkalmazás két fájlt tartalmaz:

* `pom.xml`: Ez a fájl határozza meg a projektfüggőségeket, a Java-verziót és a csomagolási módszereket.
* `Stream.java`: Ez a fájl valósítja meg a streamelési logikát.

### <a name="pomxml"></a>Pom.xml

A `pom.xml` fájl fontosabb elemei a következők:

* Függőségek: A projekt a Kafka Streams API-jára épül, amelyet a `kafka-clients` csomag tartalmaz. Ezt a függőséget a következő XML-kód határozza meg:

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

    * `maven-compiler-plugin`: Ezzel állítható a projekt által használt Java-verzió a 8-as verzióra. A HDInsight 3.6-hoz Java 8 szükséges.
    * `maven-shade-plugin`: Az alkalmazást tartalmazó über jar, valamint a függőségek létrehozásához használatos. Az alkalmazás belépési pontjának beállítására is használható, így a fő osztály megadása nélkül is közvetlenül futtatható a jar-fájl.

### <a name="streamjava"></a>Stream.java

A [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) fájl a Streams API-val implementálja a szószámláló alkalmazást. Adatokat olvas egy `test` nevű Kafka-témakörből, és a szószámokat egy `wordcounts` nevű témakörbe írja.

A következő kód definiálja a szószámláló alkalmazást:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

A projekt összeállításához és a Kafka on HDInsight-fürtön való üzembe helyezéséhez hajtsa végre a következő lépéseket:

1. Állítsa be az aktuális könyvtárat a `hdinsight-kafka-java-get-started-master\Streaming` könyvtár helyére, majd használja a következő parancsot egy jar-csomag létrehozásához:

    ```cmd
    mvn clean package
    ```

    Ez a parancs a `target/kafka-streaming-1.0-SNAPSHOT.jar` helyen hozza létre a csomagot.

2. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `clustername` elemet a fürt nevére. A következő parancs használatával másolja a `kafka-streaming-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtre. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Apache Kafka témakörök létrehozása

1. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Nyisson meg egy SSH-kapcsolatokat a fürttel a következő parancs beírásával. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Telepítse a [jQ](https://stedolan.github.io/jq/)parancssori JSON-processzort. Az Open SSH-kapcsolatban adja meg a következő parancsot a telepítéséhez `jq` :

    ```bash
    sudo apt -y install jq
    ```

3. Jelszó-változó beállítása. Cserélje le a `PASSWORD` nevet a fürt bejelentkezési jelszavára, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'
    ```

4. Helyesen kibontott fürt neve. A fürt nevének tényleges burkolata különbözhet attól függően, hogy a fürt hogyan lett létrehozva. Ez a parancs beolvassa a tényleges burkolatot, majd egy változóban tárolja. Írja be a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Ha ezt a folyamatot a fürtön kívülről hajtja végre, a fürt nevének tárolására eltérő eljárás szükséges. A fürt nevének lekérése kisbetűvel a Azure Portalból. Ezután helyettesítse be a fürt nevét `<clustername>` a következő parancsban, és hajtsa végre: `export clusterName='<clustername>'` .  

5. A Kafka-közvetítő gazdagépek és az Apache Zookeeper gazdagépek beszerzéséhez használja a következő parancsokat. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ezeknek a parancsoknak Ambari-hozzáférésre van szükségük. Ha a fürt egy NSG mögött található, akkor futtassa ezeket a parancsokat egy olyan gépről, amely hozzáfér a Ambari.

6. A streamelési művelet által használt témakörök létrehozásához használja az alábbi parancsokat:

    > [!NOTE]  
    > Hibaüzenetet kaphat, miszerint a `test` témakör már létezik. Ez rendben van, mivel lehetséges, hogy a témakör az előállítói és fogyasztói API-val foglalkozó oktatóanyagban már létre lett hozva.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    A témaköröket a következő célokra használjuk:

   * `test`: A témakör, ahová a rekordok érkeznek. A streamelési alkalmazás innen olvassa az adatokat.
   * `wordcounts`: A témakör, amelyben a streamelési alkalmazás a kimenetet tárolja.
   * `RekeyedIntermediateTopic`: Ez a témakör szolgál az adatok újraparticionálására, ahogy a `countByKey` operátor frissíti a számlálót.
   * `wordcount-example-Counts-changelog`: Ez a témakör egy, a `countByKey` művelet által használt állapottároló.

    A Kafka on HDInsight a témakörök automatikus létrehozására is konfigurálható. További információkért tekintse meg [a témakörök automatikus létrehozásának konfigurálását](apache-kafka-auto-create-topics.md) ismertető dokumentumot.

## <a name="run-the-code"></a>A kód futtatása

1. A streamelési alkalmazás háttérfolyamatként való indításához használja a következő parancsot:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Az Apache log4j figyelmeztetést kaphat. Ezt figyelmen kívül hagyhatja.

2. A bejegyzések a `test` témakörbe való küldéséhez az alábbi parancs használatával indítsa el az előállító alkalmazást:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Miután az előállító végzett, az alábbi parancs használatával tekintheti meg a `wordcounts` témakörben tárolt adatokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    A `--property` paraméterek utasítják a konzolfogyasztót, hogy a kulcsot (szó) a számlálóval (érték) együtt írja ki. Ez a paraméter emellett az értékek a Kafkából való beolvasásához használandó deszerializálót is konfigurálja.

    A kimenet az alábbi szöveghez hasonló:

    ```output
    dwarfs  13635
    ago     13664
    snow    13636
    dwarfs  13636
    ago     13665
    a       13803
    ago     13666
    a       13804
    ago     13667
    ago     13668
    jumped  13640
    jumped  13641
    ```

    A `--from-beginning` paraméter arra konfigurálja a fogyasztót, hogy a témakörben tárolt rekordok elején kezdjen. A számláló minden egyes szó beolvasásával nő, így a témakör az egyes szavakhoz több bejegyzést is tartalmaz, növekvő számlálóval.

4. A __Ctrl + C__ billentyűparanccsal zárhatja be az előállítót. A __Ctrl + C__ billentyűparancs ismételt lenyomásával zárhatja be az alkalmazást és a fogyasztót is.

5. Az adatfolyam-művelet által használt témakörök törléséhez használja a következő parancsokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan használhatja a Apache Kafka Streams API-t a Kafka on HDInsight. A következő paranccsal többet tudhat meg a Kafka használatáról.

> [!div class="nextstepaction"]
> [Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)