---
title: Apache Kafka használata a HDInsight az Azure-ban IoT Hub
description: Ismerje meg, hogyan használhatja a Apache Kafkat a HDInsight az Azure IoT Hub használatával. A Kafka csatlakozás Azure IoT Hub projekt egy forrás-és fogadó-összekötőt biztosít a Kafka számára. A forrás-összekötő beolvashatja az IoT Hubból származó adatot, és a fogadó összekötő IoT Hubba ír.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: feada70c7a461bb4a9cd621c76b5606a7f0e19d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865281"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Apache Kafka használata a HDInsight az Azure-ban IoT Hub

Megtudhatja, hogyan helyezheti át az adatátviteli [Apache Kafka az azure IoT hub](https://github.com/Azure/toketi-kafka-connect-iothub) Connectort a HDInsight és az azure IoT hub Apache Kafka között. Ebből a dokumentumból megtudhatja, hogyan futtathatja a IoT Hub-összekötőt a fürt egyik peremhálózati csomópontján.

A Kafka összekapcsolási API lehetővé teszi olyan összekötők megvalósítását, amelyek folyamatosan lekérik az adatokról a Kafka-ba, vagy egy másik rendszerbe küldenek adatokból Az [azure IoT Hub Apache Kafka csatlakoztatása](https://github.com/Azure/toketi-kafka-connect-iothub) egy összekötő, amely az IoT hub Azure-ból származó adatok lekérését kéri a Kafka-ból. Emellett az adatok a Kafka-ből a IoT Hubba való leküldését is lehetővé teszi.

A IoT Hub való húzáskor a __forrás__ -összekötőt kell használnia. IoT Hubra való továbbításkor a __fogadó összekötőt__ kell használnia. A IoT Hub összekötő a forrás és a fogadó összekötőt is biztosítja.

Az alábbi ábrán az Azure IoT Hub és a Kafka on HDInsight közötti adatfolyam látható az összekötő használatakor.

:::image type="content" source="./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png" alt-text="Kép, amely a IoT Hubról a Kafka-re áramló adatokra mutat" border="false":::

A csatlakozási API-val kapcsolatos további információkért lásd: [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Kafka-fürt a HDInsight-on. További információkért lásd [a Kafka on HDInsight használatába bevezető](apache-kafka-get-started.md) dokumentumot.

* Egy peremhálózati csomópont a Kafka-fürtben. További információ: [Edge-csomópontok használata HDInsight](../hdinsight-apps-use-edge-node.md) -dokumentummal.

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Azure-IoT Hub és-eszköz. Ebben a cikkben érdemes lehet a [Kapcsolódás a málna PI online szimulátorhoz az Azure IoT hub](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md).

* A [Scala Build eszköz](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Az összekötő létrehozása

1. Töltse le a-összekötő forrását a [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) helyi környezetbe.

2. A parancssorban navigáljon a `toketi-kafka-connect-iothub-master` címtárhoz. Ezután használja a következő parancsot a projekt összeállításához és előkészítéséhez:

    ```cmd
    sbt assembly
    ```

    A Build eltarthat néhány percig. A parancs létrehoz egy nevű fájlt a `kafka-connect-iothub-assembly_2.11-0.7.0.jar` `toketi-kafka-connect-iothub-master\target\scala-2.11` projekt könyvtárában.

## <a name="install-the-connector"></a>Az összekötő telepítése

1. Töltse fel a. jar-fájlt a Kafka peremhálózati csomópontjára a HDInsight-fürtön. Szerkessze az alábbi parancsot a `CLUSTERNAME` fürt tényleges neve helyett. Az SSH-felhasználói fiók és az [Edge-csomópont](../hdinsight-apps-use-edge-node.md#access-an-edge-node) nevének alapértelmezett értékei az alábbiakban láthatók, szükség szerint módosítva.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. A fájlmásolás befejezése után csatlakozzon az Edge-csomóponthoz az SSH használatával:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ha az összekötőt a Kafka `libs` -könyvtárba szeretné telepíteni, használja a következő parancsot:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Tartsa aktív SSH-kapcsolatban a további lépéseket.

## <a name="configure-apache-kafka"></a>Apache Kafka konfigurálása

Az alábbi lépésekkel konfigurálhatja a Kafka-t a peremhálózati csomópontra, ha az összekötőt önálló módban futtatja:

1. Jelszó-változó beállítása. Cserélje le a jelszót a fürt bejelentkezési jelszavára, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'
    ```

1. Telepítse a [jQ](https://stedolan.github.io/jq/) segédprogramot. a jQ megkönnyíti a Ambari-lekérdezések által visszaadott JSON-dokumentumok feldolgozását. Írja be a következő parancsot:

    ```bash
    sudo apt -y install jq
    ```

1. Szerezze be a Kafka-közvetítők címeit. Előfordulhat, hogy a fürt számos közvetítőt tartalmaz, de csak egy vagy kettőre van szüksége. A két közvetítő gazdagép címeinek lekéréséhez használja a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Másolja az értékeket későbbi használatra. A visszaadott érték az alábbi szöveghez hasonló:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Szerezze be az Apache Zookeeper-csomópontok címeit. A fürt számos Zookeeper-csomóponttal rendelkezik, de csak egy vagy két hivatkozásra van szükség. Használja az alábbi parancsot a változó címeinek tárolásához `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Az összekötő önálló módban való futtatásakor a rendszer a `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` Kafka-közvetítővel folytatott kommunikációhoz használja a fájlt. A fájl szerkesztéséhez `connect-standalone.properties` használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Végezze el a következő módosításokat:

    |Aktuális érték |Új érték | Megjegyzés |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Cserélje le az `localhost:9092` értéket az előző lépésben szereplő Broker-gazdagépekre.|Az Edge-csomópont önálló konfigurációját konfigurálja a Kafka-közvetítők megtalálásához.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Ez a módosítás lehetővé teszi, hogy tesztelje a Kafka által tartalmazott konzolos gyártót. Előfordulhat, hogy más gyártók és fogyasztók számára is különböző átalakítók szükségesek. További információ az átalakítók értékének használatáról: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Lásd fentebb.|
    |N/A|`consumer.max.poll.records=10`|Hozzáadás a fájl végéhez. Ennek a változásnak a célja, hogy megakadályozza a fogadó összekötő időtúllépéseit úgy, hogy egyszerre 10 rekordra korlátozza azt. További információ: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. A fájl mentéséhez használja a __CTRL + X billentyűkombinációt__ __, majd__ __írja be__ a következőt:.

1. Az összekötő által használt témakörök létrehozásához használja a következő parancsokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    A `iotin` és a `iotout` témakörök létezésének ellenőrzéséhez használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    A `iotin` témakör a IoT hub érkező üzenetek fogadására szolgál. A `iotout` témakör az üzenetek IoT hub való küldésére szolgál.

## <a name="get-iot-hub-connection-information"></a>IoT Hub-kapcsolatok adatainak beolvasása

Az összekötő által használt IoT hub-információk lekéréséhez kövesse az alábbi lépéseket:

1. Szerezze be az Event hub-kompatibilis végpontot és az Event hub-kompatibilis végpont nevét az IoT hub számára. Ezen információk beszerzéséhez használja a következő módszerek egyikét:

   * __A [Azure Portal](https://portal.azure.com/)__ hajtsa végre a következő lépéseket:

     1. Navigáljon a IoT Hub, és válassza a __végpontok__ lehetőséget.
     2. A __beépített végpontok__ területen válassza az __események__ lehetőséget.
     3. A __Tulajdonságok__ területen másolja a következő mezők értékét:

         * __Event hub – kompatibilis név__
         * __Event hub-kompatibilis végpont__
         * __Partíciók__

        > [!IMPORTANT]  
        > A portál végpontjának értéke tartalmazhat további szöveget, amely nem szükséges ebben a példában. A mintának megfelelő szöveg kinyerése `sb://<randomnamespace>.servicebus.windows.net/` .

   * __Az [Azure CLI](/cli/azure/get-started-with-azure-cli)__-ben használja a következő parancsot:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       A helyére írja `myhubname` be az IoT hub nevét. A válasz az alábbi szöveghez hasonló:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Szerezze be a __megosztott hozzáférési szabályzatot__ és a __kulcsot__. Ehhez a példához használja a __szolgáltatás__ kulcsát. Ezen információk beszerzéséhez használja a következő módszerek egyikét:

    * __A [Azure Portal](https://portal.azure.com/)__ hajtsa végre a következő lépéseket:

        1. Válassza a __megosztott hozzáférési házirendek__ lehetőséget, majd válassza a __szolgáltatás__ elemet.
        2. Másolja az __elsődleges kulcs__ értékét.
        3. Másolja a __kapcsolatok karakterláncát – elsődleges kulcs__ értékét.

    * __Az [Azure CLI](/cli/azure/get-started-with-azure-cli)__-ben használja a következő parancsot:

        1. Az elsődleges kulcs értékének beszerzéséhez használja a következő parancsot:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            A helyére írja `myhubname` be az IoT hub nevét. A válasz a központ szabályzatának elsődleges kulcsa `service` .

        2. A Szabályzathoz tartozó kapcsolódási karakterlánc beszerzéséhez `service` használja a következő parancsot:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            A helyére írja `myhubname` be az IoT hub nevét. A válasz a házirendhez tartozó kapcsolatok karakterlánca `service` .

## <a name="configure-the-source-connection"></a>A forrásoldali kapcsolatok konfigurálása

Ha úgy szeretné konfigurálni a forrást, hogy működjön a IoT Hubával, hajtsa végre az alábbi műveleteket egy SSH-kapcsolatban a peremhálózati csomóponttal:

1. Hozzon létre egy másolatot a `connect-iot-source.properties` fájlról a `/usr/hdp/current/kafka-broker/config/` címtárban. A fájl a toketi-Kafka-iothub projektből való letöltéséhez használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. A fájl szerkesztéséhez `connect-iot-source.properties` és az IoT hub adatainak hozzáadásához használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. A szerkesztőben keresse meg és módosítsa a következő bejegyzéseket:

    |Aktuális érték |Szerkesztés|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Cserélje le a `PLACEHOLDER` elemet a `iotin` kérdésre. Az IoT hub-ból érkezett üzenetek a következő `iotin` témakörbe kerülnek:.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Cserélje le `PLACEHOLDER` az eseményt az Event hub-kompatibilis névre.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Cserélje le `PLACEHOLDER` az eseményt az Event hub-kompatibilis végpontra.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Cserélje le a `PLACEHOLDER` elemet a `service` kérdésre.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Cserélje le a értékét a `PLACEHOLDER` házirend elsődleges kulcsára `service` .|
    |`IotHub.Partitions=PLACEHOLDER`|Cserélje le az `PLACEHOLDER` elemet az előző lépésekből származó partíciók számára.|
    |`IotHub.StartTime=PLACEHOLDER`|Cserélje le `PLACEHOLDER` egy UTC dátumra. Ez a dátum az, amikor az összekötő elkezdi az üzenetek ellenőrzését. A dátumformátum `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|Cserélje le a `100` elemet a `5` kérdésre. Ez a változás azt eredményezi, hogy az összekötő beolvassa az üzeneteket a Kafkaba, ha öt új üzenet van az IoT hub-ban.|

    A konfigurációval kapcsolatban lásd: [Kafka csatlakozás forrás-összekötő az Azure IoT hubhoz](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. A módosítások mentéséhez használja a __CTRL + X billentyűkombinációt__ __, majd__ __írja be__ a következőt:.

Az összekötő forrásának konfigurálásával kapcsolatos további információkért lásd: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>A fogadó-kapcsolatok konfigurálása

Ha úgy szeretné konfigurálni a fogadót, hogy működjön a IoT Hubával, hajtsa végre az alábbi műveleteket egy SSH-kapcsolatban a peremhálózati csomóponttal:

1. Hozzon létre egy másolatot a `connect-iothub-sink.properties` fájlról a `/usr/hdp/current/kafka-broker/config/` címtárban. A fájl a toketi-Kafka-iothub projektből való letöltéséhez használja a következő parancsot:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. A fájl szerkesztéséhez `connect-iothub-sink.properties` és az IoT hub adatainak hozzáadásához használja a következő parancsot:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. A szerkesztőben keresse meg és módosítsa a következő bejegyzéseket:

    |Aktuális érték |Szerkesztés|
    |---|---|
    |`topics=PLACEHOLDER`|Cserélje le a `PLACEHOLDER` elemet a `iotout` kérdésre. `iotout`A témakörbe írt üzenetek továbbítva lesznek az IoT hubhoz.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Cserélje le a értékét a `PLACEHOLDER` házirendhez tartozó kapcsolatok karakterláncára `service` .|

    Példa a konfigurációra: [Kafka csatlakozás fogadó összekötő az Azure IoT hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. A módosítások mentéséhez használja a __CTRL + X billentyűkombinációt__ __, majd__ __írja be__ a következőt:.

Az összekötő-fogadó konfigurálásával kapcsolatos további információkért lásd: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>A forrás-összekötő elindítása

1. A forrás-összekötő elindításához használja az alábbi parancsot egy SSH-kapcsolatban a peremhálózati csomóponttal:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Az összekötő elindítása után üzeneteket küldhet az IoT hub-nak az eszköz (ek) ről. Ahogy az összekötő beolvassa az üzeneteket az IoT hub-ból, és a Kafka-témakörben tárolja őket, az adatokat naplózza a konzolon:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Előfordulhat, hogy az összekötő indításakor számos figyelmeztetés jelenik meg. Ezek a figyelmeztetések nem okoznak problémát az IoT hub üzeneteinek fogadásával kapcsolatban.

1. Néhány perc elteltével állítsa le az összekötőt a **CTRL + C billentyűkombinációval** kétszer. Az összekötő leállítása eltarthat néhány percig.

## <a name="start-the-sink-connector"></a>A fogadó összekötő elindítása

A peremhálózati csomóponttal létesített SSH-kapcsolatban használja a következő parancsot a fogadó összekötő önálló módban való elindításához:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Az összekötő futtatásakor az alábbi szöveghez hasonló információk jelennek meg:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Az összekötő indításakor számos figyelmeztetést láthat. Ezek biztonságosan figyelmen kívül hagyhatók.

## <a name="send-messages"></a>Üzenetek küldése

Az üzenetek összekötőn keresztüli elküldéséhez kövesse az alábbi lépéseket:

1. Nyisson meg *egy második* SSH-munkamenetet a Kafka-fürtön:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Szerezze be az új SSH-munkamenethez tartozó Kafka-közvetítők címeit. Cserélje le a jelszót a fürt bejelentkezési jelszavára, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Ha üzeneteket szeretne küldeni a `iotout` témakörbe, használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Ez a parancs nem tér vissza a normál bash-parancssorba. Ehelyett a billentyűzet bemenetét küldi el a `iotout` témakörnek.

1. Ha üzenetet szeretne küldeni az eszköznek, illesszen be egy JSON-dokumentumot az SSH-munkamenetbe `kafka-console-producer` .

    > [!IMPORTANT]  
    > A `"deviceId"` bejegyzés értékét az eszköz azonosítójára kell beállítania. A következő példában az eszköz neve `myDeviceId` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    A JSON-dokumentum sémája részletesebben a következő címen olvasható: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Ha a szimulált málna PI eszközt használja, és az fut, az eszköz a következő üzenetet naplózza:

    ```output
    Receive message: Turn On
    ```

    Küldje újra a JSON-dokumentumot, de módosítsa a bejegyzés értékét `"message"` . Az új értéket az eszköz naplózza.

További információ a fogadó összekötő használatáról: [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használhatja a Apache Kafka kapcsolódási API-t a IoT Kafka-összekötő elindításához a HDInsight-en. Az alábbi hivatkozásokat követve megismerheti a Kafka használatának egyéb módjait:

* [Apache Spark használata a HDInsight Apache Kafka használatával](../hdinsight-apache-spark-with-kafka.md)
* [Apache Storm használata a HDInsight Apache Kafka használatával](../hdinsight-apache-storm-with-kafka.md)
